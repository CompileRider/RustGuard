Troubleshooting

This document helps diagnose common issues during RustGuard execution.

🔌 Proxy-Server Connection

Ensure Minecraft is running on the configured proxy listen port. Check your configuration file for the value of this property.

Make sure online-mode=false in your Minecraft server's server.properties.

Verify that the necessary ports (proxy listen port and API port) are not already in use by another application.

🧱 Database

Confirm the existence of the database file, typically rustguard.db (for SQLite).

Check directory permissions to ensure the RustGuard process can read and write to the database file's location.

For SQLite, ensure the file is readable and writable by the user running the service.

🔑 RCON

Port 25575 should be accessible, and the password configured in RustGuard must match the server's RCON password.

RCON is designed to be local only; do not expose it publicly.

🕸️ Webhooks

Webhook URL must be valid and properly formatted.

The target Discord channel (or other service) must have write permissions for the webhook.

Avoid exceeding service rate limits by checking for errors in the logs.

🧩 Diagnostic Examples

// Check RCON connection
let rcon = Rcon::connect("127.0.0.1:25575", "my_password")?;
rcon.cmd("say Test")?;


# Test local API (replace 25565 with your configured API port)
curl http://localhost:25565/api/status


🔄 Resolution Flow

graph TD
    P[Problem] --> D[Diagnosis: Check Configured Ports/Credentials]
    D --> S[Solution: Fix Port Conflict/Credential Mismatch]
    S --> V[Verification: Test Connection]
