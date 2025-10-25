# CAS AdminBot

<div align="center">

![Version](https://img.shields.io/badge/version-7.3.0-blue.svg)
![RedM](https://img.shields.io/badge/RedM-16565+-red.svg)
![License](https://img.shields.io/badge/license-Proprietary-orange.svg)
![Discord](https://img.shields.io/badge/Discord-Support-7289da.svg)

**A powerful Discord bot for RedM server administration with modular slash commands**

[Features](#features) • [Quick Start](#quick-start) • [Documentation](#documentation) • [Support](#support)

</div>

---

## 📋 Overview

**CAS AdminBot** is a comprehensive Discord bot designed specifically for RedM servers. It runs directly within your RedM server (no external hosting needed) and provides powerful moderation, whitelisting, and utility features through modern Discord slash commands.

Built with a **modular architecture**, the bot is incredibly easy to extend with custom commands, events, and addons tailored to your server's unique needs.

### Why CAS AdminBot?

- 🚀 **Standalone** - Runs in RedM, no external hosting required
- ⚡ **Modern Interface** - Uses Discord's native slash commands
- 🔧 **Highly Modular** - Add custom commands by creating a single file
- 🛡️ **Role-Based Permissions** - Fine-grained access control
- 🔄 **VorpCore Integration** - Full support for VORP framework
- 💬 **Bi-directional Staff Chat** - Discord ↔ In-Game communication
- 🎯 **Easy Setup** - Configure once and deploy

---

## ✨ Features

### Core Functionality
- ✅ **Discord Slash Commands** - Modern command interface with autocomplete
- ✅ **Player Management** - Kick, ban, message, and manage players remotely
- ✅ **Whitelist System** - Role-based Discord whitelist enforcement
- ✅ **Staff Chat** - Real-time communication between Discord and in-game
- ✅ **Auto ACE Permissions** - Grant server permissions based on Discord roles
- ✅ **Webhook Logging** - Log important events to Discord channels
- ✅ **Bot Status Updates** - Dynamic status showing player count
- ✅ **Context Menus** - Right-click commands on Discord users

### VorpCore Commands
- 💰 Player currency management (money, gold, rol)
- 🎒 Inventory management (give, take, inspect items)
- 💼 Job management (assign, remove, view)
- 👤 Player info and statistics
- ❤️ Heal and revive players
- 🔐 Permission group management

### Server Management
- 📦 Resource management (start, stop, restart)
- ⏰ Time control (set in-game time and freeze)
- 🌤️ Weather control (presets, transitions, forecasts)
- 📢 Server announcements
- 🔧 Whitelist configuration

### Extensibility
- 📝 **Modular Commands** - Add new commands by creating a single `.js` file
- 🎯 **Event System** - React to Discord and RedM events
- 🔌 **Addon System** - Build powerful extensions as classes
- 📤 **Exports** - Integrate with other RedM resources
- 🌐 **Localization** - Multi-language support

---

## 🚀 Quick Start

### Prerequisites

- RedM artifacts **build 16565+**
- Discord bot application ([Create one here](https://discord.com/developers/applications))
- **Both intents enabled** in Discord Developer Portal:
  - ✅ SERVER MEMBERS INTENT
  - ✅ MESSAGE CONTENT INTENT

### Installation

1. **Download and place** the resource in your RedM resources folder

2. **Ensure it's named** exactly `cas_adminbot`

3. **Add to server.cfg:**
   ```cfg
   ensure yarn
   ensure vorp_core  # If using VorpCore
   ensure cas_adminbot
   ```

4. **Configure config.js:**
   ```javascript
   const DiscordBotToken = "YOUR_BOT_TOKEN";
   const DiscordGuildId = "YOUR_DISCORD_SERVER_ID";
   const RedMServerName = "Your Server Name";
   ```

5. **Invite bot to Discord** using this URL (replace `YOUR-BOT-ID`):
   ```
   https://discord.com/api/oauth2/authorize?client_id=YOUR-BOT-ID&permissions=8&scope=bot%20applications.commands
   ```

6. **Start your server** and look for:
   ```
   [cas_adminbot][INF]: Bot is ready!
   ```

**That's it!** 🎉 Your bot is now running. Type `/help` in Discord to see available commands.

---

## 📚 Documentation

### 📖 [**VIEW FULL DOCUMENTATION →**](./DOCUMENTATION.md)

The complete documentation includes:

- **[Installation Guide](./DOCUMENTATION.md#installation)** - Detailed setup instructions
- **[Configuration](./DOCUMENTATION.md#configuration)** - All config options explained
- **[Commands Reference](./DOCUMENTATION.md#commands-reference)** - Complete command list with parameters
- **[Creating Commands](./DOCUMENTATION.md#creating-new-commands)** - Step-by-step guide with examples
- **[Creating Events](./DOCUMENTATION.md#creating-new-events)** - Add Discord.js event handlers
- **[Creating Addons](./DOCUMENTATION.md#creating-addons)** - Build powerful extensions
- **[Exports & Integration](./DOCUMENTATION.md#exports-and-integrations)** - Use bot features in other resources
- **[Troubleshooting](./DOCUMENTATION.md#troubleshooting)** - Common issues and solutions
- **[Advanced Usage](./DOCUMENTATION.md#advanced-usage)** - Custom localization, pagination, rate limiting

---

## 🎮 Command Categories

### General Commands
```
/help          - View all available commands
/onlinecount   - Show current player count
/server        - Display server statistics
```

### Player Management
```
/players       - List all online players
/message       - Send message to a player
/kick          - Kick a player
/kickall       - Kick all players (admin)
/kill          - Kill a player (admin)
/ban           - Ban/unban players
/announcement  - Server-wide announcement
```

### VorpCore Commands
```
/playerinfo    - Get player details
/heal          - Heal a player
/revive        - Revive a player
/currency      - Manage player money/gold
/inventory     - Manage player items
/job           - Manage player jobs
/permissions   - Manage VORP permissions
```

### Server Management
```
/resource      - Manage resources (start/stop/restart)
/time          - Set in-game time
/weather       - Control weather
/whitelist     - Configure whitelist
/embed         - Send custom embeds
```

---

## 🔧 Configuration Overview

Edit `config.js` or use convars in `server.cfg`:

```javascript
// Bot Settings
const DiscordBotToken = "YOUR_TOKEN";
const DiscordGuildId = "YOUR_GUILD_ID";

// Whitelist
const EnableWhitelistChecking = true;
const DiscordWhitelistRoleIds = "ROLE_ID_1, ROLE_ID_2";

// Staff Chat
const EnableStaffChatForwarding = true;
const DiscordStaffChannelId = "CHANNEL_ID";

// Permissions
const DiscordModRoleId = "ROLE_ID";
const DiscordAdminRoleId = "ROLE_ID";
const DiscordFounderRoleId = "ROLE_ID";

// Auto ACE Permissions
const AutoAcePermissions = {
  "group.admin": "DISCORD_ROLE_ID",
  "group.moderator": ["ROLE_ID_1", "ROLE_ID_2"],
};
```

**[See full configuration guide →](./DOCUMENTATION.md#configuration)**

---

## 🔨 Creating Custom Commands

Adding new commands is as simple as creating a single file:

**`server/commands/mycommand.js`**
```javascript
module.exports = {
  name: "mycommand",
  description: "My custom command",
  role: "mod", // user, mod, admin, founder
  
  options: [
    {
      name: "player",
      description: "Player ID",
      required: true,
      type: "INTEGER",
    },
  ],
  
  run: async (client, interaction, args) => {
    const playerId = args.player;
    const playerName = GetPlayerName(playerId);
    
    return interaction.reply({
      content: `You selected player: ${playerName}`,
      ephemeral: false,
    });
  },
};
```

**That's it!** The bot automatically loads it on restart.

**[Learn more about creating commands →](./DOCUMENTATION.md#creating-new-commands)**

---

## 🔌 Exports for Other Resources

Use CAS AdminBot features in your other RedM resources:

```lua
-- Check if player has Discord role
local hasVIP = exports['cas_adminbot']:isRolePresent(source, "VIP_ROLE_ID")

-- Get player's Discord roles
local roles = exports['cas_adminbot']:getRoles(source)

-- Get Discord name
local discordName = exports['cas_adminbot']:getName(source)

-- Get Discord ID
local discordId = exports['cas_adminbot']:getDiscordId(source)

-- Send webhook log
exports['cas_adminbot']:log("admin", {
  title = "Player Banned",
  message = "A player was banned",
  fields = {
    { name = "Player", value = playerName, inline = true },
    { name = "Reason", value = reason, inline = true },
  },
  color = 16711680, -- Red
})
```

**[View integration examples →](./DOCUMENTATION.md#exports-and-integrations)**

---

## 🛠️ Troubleshooting

### Bot Won't Start?

**Error: `[DISALLOWED_INTENTS]`**
- ✅ Enable **SERVER MEMBERS INTENT** and **MESSAGE CONTENT INTENT** in Discord Developer Portal

**Error: `[TOKEN_INVALID]`**
- ✅ Generate a new bot token and update `config.js`

**Commands don't appear?**
- ✅ Re-invite bot with correct scope using the invite URL
- ✅ Wait a few minutes for Discord to register commands
- ✅ Check `EnableDiscordSlashCommands = true` in config

**[View full troubleshooting guide →](./DOCUMENTATION.md#troubleshooting)**

---

## 📦 Project Structure

```
cas_adminbot/
├── server/
│   ├── commands/        # Slash commands (modular)
│   ├── events/          # Discord.js event handlers
│   ├── addons/          # Addon system extensions
│   ├── bot.js           # Discord bot class
│   ├── server.js        # Main entry point
│   └── utils.js         # Utility functions
├── client/
│   └── client.lua       # Client-side (minimal)
├── locales/
│   └── en.js            # Language files
├── config.js            # Configuration
├── fxmanifest.lua       # Resource manifest
├── DOCUMENTATION.md     # Full documentation
└── README.md            # This file
```

---

## 💡 Examples

### Example: Custom VIP Vehicle Lock
```lua
RegisterNetEvent('checkVehicleAccess')
AddEventHandler('checkVehicleAccess', function(vehicle)
  local source = source
  local hasVIP = exports['cas_adminbot']:isRolePresent(source, "VIP_ROLE_ID")
  
  if hasVIP then
    TriggerClientEvent('grantVehicleAccess', source, vehicle)
  else
    TriggerClientEvent('chat:addMessage', source, {
      args = {"System", "VIP role required!"}
    })
  end
end)
```

### Example: Dynamic Permissions on Join
```lua
AddEventHandler('playerJoining', function()
  local source = source
  Wait(2000)
  
  if exports['cas_adminbot']:isRolePresent(source, "ADMIN_ROLE") then
    ExecuteCommand('add_principal player.' .. source .. ' group.admin')
  end
end)
```

**[More examples in documentation →](./DOCUMENTATION.md#exports-and-integrations)**

---

## 🤝 Support

### Need Help?

- 💬 **Discord Support**: [Join our Discord](https://discord.gg/QxjVKBEqXX)
- 📖 **Documentation**: [View Full Docs](./DOCUMENTATION.md)
- 🐛 **Bug Reports**: Open a ticket on Discord
- 💡 **Feature Requests**: Suggest ideas on Discord

### Reporting Issues

When reporting bugs, please include:
1. RedM artifacts version
2. Bot version (7.3.0)
3. Complete error message
4. Steps to reproduce
5. Configuration (redact sensitive info)

---

## 📄 License

© 2025 by Brownyprod

This software (**cas_adminbot**) is licensed exclusively to the client.

**Restrictions:**
- Reselling, redistribution, or sharing with third parties is **strictly prohibited**
- The client is the sole authorized user
- Unauthorized use may result in legal action

For licensing inquiries, please contact the author.

---

## 👥 Credits

- **Author**: [brownyprod](https://github.com/Brownyprod)
- **Contributors**: [zfbx](https://github.com/zfbx)
- **Framework**: discord.js (custom fork)

---

## 🌟 Star this Repository!

If you find CAS AdminBot useful, please consider starring this repository and sharing it with other RedM server owners!

---

<div align="center">

**[📚 View Full Documentation](./DOCUMENTATION.md)** • **[💬 Join Discord](https://discord.gg/QxjVKBEqXX)** • **[🐛 Report Issues](https://discord.gg/QxjVKBEqXX)**

Made with ❤️ for the RedM community

</div>
