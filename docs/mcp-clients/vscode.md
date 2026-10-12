# VS Code

Connect Clippy Vision to Visual Studio Code using MCP and access its tools through GitHub Copilot Chat in Agent mode.

## One-click setup

1. Open Clippy Vision and go to **Settings → Connect apps**.
2. Click **Connect** in the VS Code section.
3. VS Code should open an MCP installation prompt. Review the server details before approving.
4. Check the server status and confirm the tools are available in Copilot Chat Agent mode.

Clippy Vision builds the installation URL using `buildVscodeInstallUrl` in `electron-ui/electron/lib/mcp.js`. The URL uses the `vscode:mcp/install?...` scheme and attempts to write the server configuration to the VS Code user `mcp.json` when possible.

If the prompt does not open, try the manual setup below.

**Verification status:** Not verified yet.

## Manual setup

### 1. Copy the configuration

1. Open Clippy Vision.
2. Go to **Settings → Connect apps**.
3. Click **Copy JSON**.

### 2. Open the VS Code user configuration

In VS Code, open the Command Palette and run **MCP: Open User Configuration**.

Standard configuration paths:

- **Windows:** `%APPDATA%\Code\User\mcp.json`
- **macOS:** `~/Library/Application Support/Code/User/mcp.json`
- **Linux:** `~/.config/Code/User/mcp.json`

For custom profiles or other configuration locations, consult the [official VS Code MCP documentation](https://code.visualstudio.com/docs/copilot/chat/mcp-servers).

### 3. Adapt the configuration

> **Important:** VS Code uses the top-level `servers` key, not `mcpServers`. Cursor and Claude Desktop use `mcpServers`, so their configuration cannot be pasted unchanged into VS Code's native `mcp.json`.

1. If the copied JSON uses `mcpServers`, rename the outer key to `servers`.
2. Preserve the Clippy Vision server entry, including its command, arguments, environment variables, and paths.
3. Keep existing servers and add Clippy Vision inside the existing `servers` object if one is already present.
4. Save the file.

Example of the finished VS Code `mcp.json`:

```json
{
  "servers": {
    "clippy-vision": {
      "type": "stdio",
      "command": "YOUR_COMMAND",
      "args": [],
      "env": {}
    }
  }
}
```

Replace the placeholders with the actual values from **Copy JSON**, preserving any required arguments and environment variables.

Use the actual configuration values from **Copy JSON**; do not guess paths or commands.

### 4. Check the server and tools

1. Open the Command Palette and run **MCP: List Servers**.
2. Select Clippy Vision to inspect its status. If it is stopped, start it; if it reports an error, inspect the output.
3. Open GitHub Copilot Chat and switch to **Agent** mode.
4. Open **Configure Tools** and check that Clippy Vision's tools are listed and enabled.

Refer to the [official VS Code MCP documentation](https://code.visualstudio.com/docs/copilot/chat/mcp-servers) for current commands and menus.

**Verification status:** Not verified yet.

## Test the connection

In Copilot Chat Agent mode, ask:

- **“What happened in the last hour?”**
- **“What activity was recorded recently?”**

For the first question, check whether Copilot calls `list_sessions_tool` or `search_sessions_tool`. A good answer summarizes activity returned by the tool for the requested period.

To check capture coverage, ask Copilot: **“Was Clippy Vision capturing during the last hour?”** Copilot should call `activity_coverage_tool`, which helps distinguish between “nothing happened” and “capture was off” for the requested time range. An empty activity result alone does not prove the connection is broken.

**Verification status:** Not verified yet.

## Privacy note

Copilot's model receives the information returned by Clippy Vision's tools. The Clippy Vision redactor still runs on every tool reply before it is returned to the client. Review what you share with AI clients; redaction may not detect every sensitive detail.

## References

- [VS Code MCP documentation](https://code.visualstudio.com/docs/copilot/chat/mcp-servers)
- [Clippy Vision MCP guide and tool reference](../mcp.md)
- [Clippy Vision MCP connection implementation](../../electron-ui/electron/lib/mcp.js)
