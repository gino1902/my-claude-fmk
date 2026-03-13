# MCP Filesystem Setup — WSL2 + Claude Desktop

**Environment:** Windows 11 + WSL2 + Claude Desktop v1.1.4498

## Steps

### 1. Install Filesystem Extension
- Go to **Settings → Extensions → Browse extensions**
- Find **Filesystem** in the Anthropic-reviewed directory
- Click **Install**

### 2. Configure WSL2 Path
When prompted for the directory path, enter the WSL2 UNC format:
```
\\wsl.localhost\Ubuntu\home\gino\workspace\slide-gen
```

### 3. Restart Claude Desktop
Fully quit via tray icon → Quit, then relaunch.

## Verify Connection
In a new chat, ask Claude to list files in your repo. If it returns your file tree → ✅ connected.

## Notes
- No manual `claude_desktop_config.json` editing required
- No Node.js/npm installation required — Claude Desktop includes a built-in Node.js environment
- WSL2 paths must use UNC format: `\\wsl.localhost\<distro>\...`

## Troubleshooting
If the extension connects but tools aren't available:
- Enable Developer Mode → **Help → Enable Developer Mode**
- Check logs at **Settings → Developer** to see connection status and errors
- Restart Claude Desktop to refresh the extension registry
