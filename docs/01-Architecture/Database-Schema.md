# Database Schema

## Entity Relationship Diagram

```sql
erDiagram
    PLAYERS ||--o{ DETECTIONS : "has many"
    PLAYERS ||--o{ SESSIONS : "participates in"
    PLAYERS ||--|| PLAYER_STATS : "has one"
    DETECTIONS }o--|| DETECTION_TYPES : "is of type"
    
    PLAYERS {
        bigint id PK
        varchar username UK "Minecraft username"
        varchar uuid UK "Player UUID"
        timestamp first_seen "First connection"
        timestamp last_seen "Last connection"
        boolean banned "Ban status"
        timestamp banned_at "Ban timestamp"
        varchar banned_reason "Ban reason"
    }
    
    DETECTIONS {
        bigint id PK
        bigint player_id FK
        int detection_type_id FK
        float confidence "0.0-1.0"
        json context "Detection context"
        varchar action_taken "kick/ban/log"
        timestamp detected_at
        bigint reviewed_by FK "Admin who reviewed"
        varchar review_status "pending/approved/false_positive"
        text review_notes
    }
    
    DETECTION_TYPES {
        int id PK
        varchar name UK "speed_hack/fly_hack/etc"
        varchar description
        int default_severity "1-10"
        boolean enabled
    }
    
    SESSIONS {
        bigint id PK
        bigint player_id FK
        timestamp connected_at
        timestamp disconnected_at
        int packets_sent
        int packets_received
        int detections_count "Detections this session"
        varchar disconnect_reason
    }
    
    PLAYER_STATS {
        bigint player_id PK FK
        int total_playtime_seconds
        int total_detections
        int false_positive_count
        float trust_score "0.0-1.0"
        timestamp last_updated
        json additional_stats "Flexible stats"
    }
```

## Table Definitions

### players

```sql
CREATE TABLE players (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT NOT NULL UNIQUE,
    uuid TEXT NOT NULL UNIQUE,
    first_seen INTEGER NOT NULL,
    last_seen INTEGER NOT NULL,
    banned BOOLEAN NOT NULL DEFAULT 0,
    banned_at INTEGER,
    banned_reason TEXT
);

CREATE INDEX idx_players_username ON players(username);
CREATE INDEX idx_players_uuid ON players(uuid);
CREATE INDEX idx_players_banned ON players(banned);
```

### detections

```sql
CREATE TABLE detections (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    player_id INTEGER NOT NULL,
    detection_type_id INTEGER NOT NULL,
    confidence REAL NOT NULL,
    context TEXT NOT NULL, -- JSON
    action_taken TEXT NOT NULL,
    detected_at INTEGER NOT NULL,
    reviewed_by INTEGER,
    review_status TEXT DEFAULT 'pending',
    review_notes TEXT,
    FOREIGN KEY (player_id) REFERENCES players(id),
    FOREIGN KEY (detection_type_id) REFERENCES detection_types(id)
);

CREATE INDEX idx_detections_player ON detections(player_id);
CREATE INDEX idx_detections_type ON detections(detection_type_id);
CREATE INDEX idx_detections_time ON detections(detected_at);
CREATE INDEX idx_detections_confidence ON detections(confidence);
```

### detection_types

```sql
CREATE TABLE detection_types (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL UNIQUE,
    description TEXT,
    default_severity INTEGER NOT NULL DEFAULT 5,
    enabled BOOLEAN NOT NULL DEFAULT 1
);

-- Initial data
INSERT INTO detection_types (name, description, default_severity) VALUES
    ('speed_hack', 'Moving faster than possible', 7),
    ('fly_hack', 'Flying without permission', 9),
    ('killaura', 'Automated combat', 8),
    ('xray', 'Mining patterns suggest xray', 6),
    ('auto_clicker', 'Inhuman click patterns', 5);
```

### sessions

```sql
CREATE TABLE sessions (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    player_id INTEGER NOT NULL,
    connected_at INTEGER NOT NULL,
    disconnected_at INTEGER,
    packets_sent INTEGER DEFAULT 0,
    packets_received INTEGER DEFAULT 0,
    detections_count INTEGER DEFAULT 0,
    disconnect_reason TEXT,
    FOREIGN KEY (player_id) REFERENCES players(id)
);

CREATE INDEX idx_sessions_player ON sessions(player_id);
CREATE INDEX idx_sessions_connected ON sessions(connected_at);
```

### player_stats

```sql
CREATE TABLE player_stats (
    player_id INTEGER PRIMARY KEY,
    total_playtime_seconds INTEGER DEFAULT 0,
    total_detections INTEGER DEFAULT 0,
    false_positive_count INTEGER DEFAULT 0,
    trust_score REAL DEFAULT 0.5,
    last_updated INTEGER NOT NULL,
    additional_stats TEXT, -- JSON
    FOREIGN KEY (player_id) REFERENCES players(id)
);
```

## Common Queries

### Top Cheaters

```sql
SELECT
    p.username,
    COUNT(d.id) as detection_count,
    AVG(d.confidence) as avg_confidence
FROM players p
JOIN detections d ON p.id = d.player_id
WHERE d.detected_at > strftime('%s', 'now', '-7 days')
GROUP BY p.id
ORDER BY detection_count DESC
LIMIT 10;
```

### Detection Summary

```sql
SELECT
    dt.name,
    COUNT(d.id) as count,
    AVG(d.confidence) as avg_confidence,
    SUM(CASE WHEN d.action_taken = 'kick' THEN 1 ELSE 0 END) as kicks,
    SUM(CASE WHEN d.action_taken = 'ban' THEN 1 ELSE 0 END) as bans
FROM detection_types dt
LEFT JOIN detections d ON dt.id = d.detection_type_id
WHERE d.detected_at > strftime('%s', 'now', '-24 hours')
GROUP BY dt.id;
```

### Player History

```sql
SELECT
    d.detected_at,
    dt.name as cheat_type,
    d.confidence,
    d.action_taken,
    d.context
FROM detections d
JOIN detection_types dt ON d.detection_type_id = dt.id
WHERE d.player_id = ?
ORDER BY d.detected_at DESC
LIMIT 50;
```

### Trust Score Calculation

```sql
UPDATE player_stats
SET trust_score = (
    0.5 -- Base
    + (total_playtime_seconds / 3600.0 * 0.01) -- +0.01 per hour
    - (total_detections * 0.1) -- -0.1 per detection
    + (false_positive_count * 0.05) -- +0.05 per FP (benefit of doubt)
),
last_updated = strftime('%s', 'now')
WHERE player_id = ?;
```

## Data Retention

### Recommended Policies

```sql
-- Delete old sessions (keep 30 days)
DELETE FROM sessions
WHERE disconnected_at < strftime('%s', 'now', '-30 days');

-- Archive old detections (keep 90 days)
DELETE FROM detections
WHERE detected_at < strftime('%s', 'now', '-90 days')
AND review_status != 'pending';

-- Never delete: players, player_stats
```

## Backup Strategy

```bash
# Daily backup
sqlite3 rustguard.db ".backup rustguard_backup_$(date +%Y%m%d).db"

# Weekly archive
tar -czf rustguard_$(date +%Y%m%d).tar.gz rustguard.db logs/
```

## Performance Tips

1. **Use indexes** - All FKs indexed
2. **Batch inserts** - Use transactions
3. **WAL mode** - Better concurrency
4. **Pragma optimize** - Run periodically

```sql
PRAGMA journal_mode = WAL;
PRAGMA synchronous = NORMAL;
PRAGMA cache_size = -64000; -- 64MB cache
PRAGMA temp_store = MEMORY;
```

## Related Documents

* [Architecture/Overview](./Overview.md)
* [Core-Components/Action-Handler](./docs/02-Core-Components/Action-Handler.md)
