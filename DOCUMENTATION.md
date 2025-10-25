# CAS AdminBot - Complete Documentation

## Table of Contents
1. [Introduction](#introduction)
2. [Features](#features)
3. [Requirements](#requirements)
4. [Installation](#installation)
5. [Configuration](#configuration)
6. [Commands Reference](#commands-reference)
7. [Creating New Commands](#creating-new-commands)
8. [Creating New Events](#creating-new-events)
9. [Creating Addons](#creating-addons)
10. [Exports and Integrations](#exports-and-integrations)
11. [Logging System](#logging-system)
12. [Troubleshooting](#troubleshooting)
13. [Advanced Usage](#advanced-usage)

---

## Introduction

**CAS AdminBot** is a comprehensive Discord bot designed specifically for RedM server administration. It runs directly within your RedM server (no external hosting required) and provides powerful moderation, whitelisting, and utility features through Discord slash commands.

The bot is built on a **modular architecture**, making it extremely easy to extend with custom commands, events, and addons tailored to your server's needs.

### Version Information
- **Current Version**: 1.0.0
- **Minimum RedM Artifacts**: Build 16565 or newer
- **Node.js Version**: v12.13.0 (bundled with RedM)
- **Framework**: discord.js (custom fork)

---

## Features

### Core Features
✅ **Standalone Resource** - Runs directly in RedM, no external hosting needed  
✅ **Slash Commands** - Modern Discord slash command interface with autocomplete  
✅ **Moderation Tools** - Kick, ban, message, and manage players remotely  
✅ **Whitelist System** - Role-based Discord whitelist enforcement  
✅ **Staff Chat** - Bi-directional staff communication between Discord and in-game  
✅ **Auto ACE Permissions** - Automatically grant server permissions based on Discord roles  
✅ **VorpCore Integration** - Full support for VorpCore framework commands  
✅ **Webhook Logging** - Log important events to Discord webhooks  
✅ **Bot Status Messages** - Dynamic status updates showing player count  
✅ **Modular Architecture** - Easy to extend with custom commands and features  

### Permission System
- **Role-based access control** with four permission levels:
  - `user` - All Discord members
  - `mod` - Moderators
  - `admin` - Administrators
  - `founder` - Server owner/founders

---

## Requirements

### RedM Server Requirements
- **RedM artifacts** build 4890 or higher (16565+ recommended)
- **cfx-server-data** resource pack installed (specifically `yarn` at minimum)
- Server running on Windows or Linux

### Discord Requirements
- Discord bot application with bot token
- Discord server (guild) with manage server permissions
- **Both privileged intents enabled** on Discord Developer Portal:
  - ✅ **SERVER MEMBERS INTENT**
  - ✅ **MESSAGE CONTENT INTENT**

### Optional Requirements
- **VorpCore** framework (for VORP-specific commands)

---

## Installation

### Step 1: Create Discord Bot Application

1. Go to [Discord Developer Portal](https://discord.com/developers/applications)
2. Click "New Application" and give it a name
3. Navigate to the **Bot** section in the left sidebar
4. Click "Add Bot"
5. **CRITICAL**: Under "Privileged Gateway Intents", enable:
   - ✅ **SERVER MEMBERS INTENT**
   - ✅ **MESSAGE CONTENT INTENT**
6. Copy the bot token (you'll need this for configuration)

### Step 2: Invite Bot to Your Discord Server

Replace `YOUR-BOT-ID` with your bot's Application ID and visit this URL:

```
https://discord.com/api/oauth2/authorize?client_id=YOUR-BOT-ID&permissions=8&scope=bot%20applications.commands
```

**Important**: This link grants administrator permissions and slash command scope. If your bot is already in the server, you MUST run this link again to add the `applications.commands` scope.

### Step 3: Install Resource

1. Download/copy the `cas_adminbot` resource
2. Place it in your RedM resources directory
3. **Ensure the folder is named exactly** `cas_adminbot` (not `cas_adminbot-main` or anything else)
4. Verify that `[system]/[builders]/yarn/` exists in your resources

### Step 4: Configure Server.cfg

Add the following to your `server.cfg`:

```cfg
# Ensure yarn is loaded first
ensure yarn

# If using VorpCore, ensure it's loaded before cas_adminbot
ensure vorp_core

# Load CAS AdminBot
ensure cas_adminbot
```

### Step 5: Configure the Bot

Edit `config.js` and configure at minimum:

```javascript
const DiscordBotToken = "YOUR_BOT_TOKEN_HERE";
const DiscordGuildId = "YOUR_DISCORD_SERVER_ID";
const RedMServerName = "Your Server Name";
```

See [Configuration](#configuration) for full configuration options.

### Step 6: Start Your Server

Start your RedM server and check the console for:

```
[cas_adminbot][TIMESTAMP][INF]: Bot is ready!
```

If you see errors, check the [Troubleshooting](#troubleshooting) section.

---

## Configuration

All configuration is done in `config.js`. You can also override these settings using convars in your `server.cfg`.

### General Settings

```javascript
const LanguageLocaleCode = "en";
const RedMServerName = "Your Server Name";
const DiscordInviteLink = "https://discord.gg/yourcode";
const RedMServerIP = "127.0.0.1";
const DebugLogs = false; // Enable for debugging (very spammy)
```

### Discord Bot Settings

```javascript
const EnableDiscordBot = true;
const DiscordBotToken = "YOUR_BOT_TOKEN";
const DiscordGuildId = "YOUR_GUILD_ID";
```

### Staff Chat Settings

```javascript
const EnableStaffChatForwarding = true;
const DiscordStaffChannelId = "CHANNEL_ID_HERE";
const AdditionalStaffChatRoleIds = [
  "ROLE_ID_1",
  "ROLE_ID_2",
];
```

When enabled, staff can use `/staff <message>` in-game and it forwards to Discord. Discord messages in the staff channel are sent to in-game staff.

### Whitelist Settings

```javascript
const EnableWhitelistChecking = true;
const DiscordWhitelistRoleIds = "ROLE_ID_1, ROLE_ID_2, ROLE_ID_3";
```

Players must have Discord open and one of these roles to join the server.

### Slash Command Settings

```javascript
const EnableDiscordSlashCommands = true;
const DiscordModRoleId = "ROLE_ID";
const DiscordAdminRoleId = "ROLE_ID";
const DiscordFounderRoleId = "ROLE_ID";
```

These roles determine who can use which commands based on the command's `role` property.

### Bot Status Messages

```javascript
const EnableBotStatusMessages = true;
const BotStatusMessages = [
  "Welcome to {servername}!",
  "{playercount} players online.",
  "Join at {invite}",
];
```

Cycles through status messages. Supports placeholders:
- `{servername}` - Server name
- `{playercount}` - Current player count
- `{invite}` - Discord invite link

### Auto ACE Permissions

```javascript
const EnableAutoAcePermissions = true;
const AutoAcePermissions = {
  "group.admin": "DISCORD_ROLE_ID",
  "group.moderator": ["ROLE_ID_1", "ROLE_ID_2"],
  "my.custom.permission": "ROLE_ID",
};
```

Automatically grants FiveM ACE permissions to players when they join if they have the corresponding Discord role.

### Webhook Logging

```javascript
const EnableLoggingWebhooks = true;
const LoggingWebhookName = "Server Logs";
const LoggingAlertPingId = "&ROLE_ID"; // Use & for roles, leave blank for none
const LoggingWebhooks = {
  "bank": "https://discord.com/api/webhooks/...",
  "admin": "https://discord.com/api/webhooks/...",
  "death": "https://discord.com/api/webhooks/...",
};
```

Other scripts can trigger webhook logs using the logging addon (see [Addons](#creating-addons)).

### Using Convars (Alternative Configuration)

Instead of editing `config.js`, you can set these in `server.cfg`:

```cfg
set discord_token "YOUR_BOT_TOKEN"
set discord_guild_id "YOUR_GUILD_ID"
set discord_server_name "My Server"
set discord_invite "https://discord.gg/yourcode"
set discord_enable_bot true
set discord_enable_whitelist true
set discord_whitelist_roles "ROLE_ID_1,ROLE_ID_2"
set discord_mod_role "ROLE_ID"
set discord_admin_role "ROLE_ID"
set discord_founder_role "ROLE_ID"
set discord_enable_staff_chat true
set discord_staff_channel_id "CHANNEL_ID"
set discord_enable_commands true
set discord_enable_status true
set discord_enable_ace_perms true
set discord_enable_logging_webhooks true
```

---

## Commands Reference

### General Commands

| Command | Description | Permission | Options |
|---------|-------------|------------|---------|
| `/help` | Show all available commands with pagination | `user` | None |
| `/onlinecount` | Display current online player count | `user` | None |
| `/server` | Show detailed server and Discord statistics | `user` | None |

### Player Management Commands

| Command | Description | Permission | Options |
|---------|-------------|------------|---------|
| `/players` | List all online players with pagination | `mod` | None |
| `/message` | Send direct message to a player | `mod` | `id` (player ID), `message` (text) |
| `/kick` | Kick a player from the server | `mod` | `id` (player ID), `message` (optional reason) |
| `/kickall` | Kick all players from the server | `admin` | `message` (kick reason) |
| `/kill` | Kill a player in-game | `admin` | `id` (player ID) |
| `/ban` | Manage server bans | `admin` | **Subcommands**:<br/>• `add`: Ban a player (`id`, `reason`)<br/>• `remove`: Unban (`license`)<br/>• `list`: Show all bans |
| `/announcement` | Send server-wide announcement | `mod` | `message` (announcement text) |

**Context Menu Commands:**
- Right-click a Discord user → Apps → `Check Online` - See if they're currently in-game (requires `mod` role)

### VorpCore Commands

These commands require VorpCore to be installed and loaded before cas_adminbot.

| Command | Description | Permission | Subcommands/Options |
|---------|-------------|------------|---------------------|
| `/playerinfo` | Get detailed player information | `admin` | `id` (player ID) |
| `/heal` | Heal a player to full health | `admin` | `id` (player ID) |
| `/revive` | Revive and fully heal a player | `admin` | `id` (player ID) |
| `/currency` | Manage player money/gold/rol | `admin` | **Subcommands**:<br/>• `give`: Add currency (`id`, `type`, `amount`)<br/>• `take`: Remove currency (`id`, `type`, `amount`) |
| `/inventory` | Manage player inventory | `admin` | **Subcommands**:<br/>• `give`: Add item (`id`, `item`, `count`)<br/>• `take`: Remove item (`id`, `item`, `count`)<br/>• `inspect`: View inventory (`id`) |
| `/job` | Manage player jobs | `admin` | **Subcommands**:<br/>• `set`: Assign job (`id`, `job`, `label`)<br/>• `remove`: Remove job (`id`)<br/>• `view`: View current job (`id`) |
| `/permissions` | Manage VORP permission groups | `founder` | **Subcommands**:<br/>• `add`: Grant permission (`id`, `permission`)<br/>• `remove`: Revoke permission (`id`)<br/>• `view`: View permissions (`id`) |

### Server Management Commands

| Command | Description | Permission | Subcommands/Options |
|---------|-------------|------------|---------------------|
| `/resource` | Manage server resources/scripts | `founder` | **Subcommands**:<br/>• `start`: Start a resource<br/>• `stop`: Stop a resource<br/>• `ensure`: Restart a resource<br/>• `refresh`: Reload all resources<br/>• `list`: List running resources<br/>• `inspect`: View resource info |
| `/time` | Set in-game time | `admin` | `day`, `hour`, `minute`, `second`, `freeze` (boolean) |
| `/weather` | Manage in-game weather | `admin` | **Subcommands**:<br/>• `get`: Show current weather<br/>• `set`: Change weather (`preset`, `transition`, `freeze`, `permanentsnow`)<br/>• `forecast`: View forecast |
| `/whitelist` | Configure whitelist settings | `founder` | **Subcommands**:<br/>• `toggle`: Enable/disable (`enabled`)<br/>• `addrole`: Add role to whitelist<br/>• `removerole`: Remove role from whitelist |

### Discord Utility Commands

| Command | Description | Permission | Options |
|---------|-------------|------------|---------|
| `/embed` | Send embedded messages to channels | `founder` | **Subcommands**:<br/>• `simple`: Quick embed (`channel`, `message`, `title`, `image`, `thumbnail`, `footer`, `color`)<br/>• `complex`: JSON embed (`channel`, `json`) |

### In-Game Commands

When staff chat is enabled:

| Command | Description | Permission |
|---------|-------------|------------|
| `/staff <message>` | Send message to staff chat | ACE: `cas_adminbot.staffchat` |
| `/stafftoggle` | Toggle staff chat messages on/off | Discord staff role |

---

## Creating New Commands

The bot uses a **modular command system**. Each command is a separate `.js` file in the `server/commands/` directory.

### Command Structure

Create a new file `server/commands/yourcommand.js`:

```javascript
module.exports = {
  // Required: Command name (lowercase, no spaces)
  name: "yourcommand",
  
  // Required: Description shown in /help and Discord
  description: "What your command does",
  
  // Required: Permission level (user, mod, admin, founder)
  role: "mod",
  
  // Optional: Command type (CHAT_INPUT by default)
  // Types: CHAT_INPUT, USER (context menu), MESSAGE (context menu)
  type: "CHAT_INPUT",
  
  // Optional: Slash command options
  options: [
    {
      name: "parameter",
      description: "Description of this parameter",
      required: true,
      type: "STRING", // STRING, INTEGER, BOOLEAN, USER, CHANNEL, ROLE
    },
    {
      name: "optional_param",
      description: "Optional parameter",
      required: false,
      type: "INTEGER",
    },
  ],
  
  // Required: Command execution function
  run: async (client, interaction, args) => {
    // Access command arguments
    const param = args.parameter;
    const optionalParam = args.optional_param || 0;
    
    // Access bot utilities
    const playerName = GetPlayerName(args.id);
    client.utils.log.info(`Command executed by ${interaction.member.displayName}`);
    
    // Reply to interaction
    return interaction.reply({
      content: `Result: ${param}`,
      ephemeral: false, // true = only visible to command user
    });
  },
};
```

### Option Types

| Type | Description | Example |
|------|-------------|---------|
| `STRING` | Text input | User types "hello world" |
| `INTEGER` | Whole number | 42, 100, -5 |
| `NUMBER` | Decimal number | 3.14, 2.5 |
| `BOOLEAN` | True/false toggle | true, false |
| `USER` | Discord user picker | @Username |
| `CHANNEL` | Discord channel picker | #general |
| `ROLE` | Discord role picker | @Moderator |
| `SUB_COMMAND` | Creates subcommands | `/parent subcommand` |
| `SUB_COMMAND_GROUP` | Groups subcommands | `/parent group subcommand` |

### Subcommands Example

```javascript
module.exports = {
  name: "manage",
  description: "Manage server features",
  role: "admin",
  
  options: [
    {
      name: "players",
      description: "Manage players",
      type: "SUB_COMMAND_GROUP",
      options: [
        {
          name: "kick",
          description: "Kick a player",
          type: "SUB_COMMAND",
          options: [
            {
              name: "id",
              description: "Player ID",
              required: true,
              type: "INTEGER",
            },
          ],
        },
        {
          name: "ban",
          description: "Ban a player",
          type: "SUB_COMMAND",
          options: [
            {
              name: "id",
              description: "Player ID",
              required: true,
              type: "INTEGER",
            },
          ],
        },
      ],
    },
  ],
  
  run: async (client, interaction, args) => {
    // Check which subcommand was used
    if (args.kick) {
      // Handle kick subcommand
      DropPlayer(args.id, "Kicked by staff");
      return interaction.reply({ content: "Player kicked!" });
    }
    
    if (args.ban) {
      // Handle ban subcommand
      // Your ban logic here
      return interaction.reply({ content: "Player banned!" });
    }
  },
};
```

### Accessing Bot Features in Commands

```javascript
run: async (client, interaction, args) => {
  // Bot configuration
  const config = client.config;
  
  // Bot utilities
  const utils = client.utils;
  utils.log.info("Information message");
  utils.log.warn("Warning message");
  utils.log.error("Error message");
  
  // Sleep/delay
  await utils.sleep(1000); // Wait 1 second
  
  // Get player identifiers
  const ids = utils.getPlayerIdentifiers(source);
  // Returns: { license: "...", discord: "...", steam: "..." }
  
  // Get Discord ID from source
  const discordId = utils.getPlayerDiscordId(source);
  
  // Get source from Discord ID
  const source = await utils.getPlayerFromDiscordId(discordId);
  
  // Get Discord member from source
  const member = client.getMemberFromSource(source);
  
  // Check if member has role
  const hasRole = client.isRolePresent(member, "ROLE_ID");
  
  // Get member's roles
  const roles = client.getMemberRoles(member);
  
  // Send chat message to player
  utils.chatMessage(source, "Label", "Message", {
    color: [255, 0, 0],
    multiline: false,
  });
  
  // Send to all players
  utils.chatMessage(-1, "Server", "Global announcement");
  
  // Replace global variables
  const message = utils.replaceGlobals(client, 
    "Welcome to {servername}! {playercount} players online."
  );
  
  // VorpCore (if available)
  if (client.VorpCore) {
    const User = client.VorpCore.getUser(source);
    const Character = User.getUsedCharacter;
    // Use VorpCore API as normal
  }
  
  // Create embeds
  const embed = new client.Embed()
    .setTitle("Title")
    .setDescription("Description")
    .setColor(0x00FF00)
    .addField("Field", "Value", true)
    .setFooter({ text: "Footer" })
    .setTimestamp();
    
  return interaction.reply({ embeds: [embed] });
}
```

### Context Menu Commands

Create a command that appears when right-clicking users or messages:

```javascript
module.exports = {
  name: "Check Player Info",
  role: "mod",
  type: "USER", // USER for user context menu, MESSAGE for message context menu
  
  run: async (client, interaction) => {
    // For USER type, get the targeted user
    const targetUser = interaction.targetMember;
    const discordId = targetUser.id;
    
    // Get their source if online
    const source = await client.utils.getPlayerFromDiscordId(discordId);
    
    if (!source) {
      return interaction.reply({
        content: `${targetUser.displayName} is not currently online.`,
        ephemeral: true,
      });
    }
    
    const playerName = GetPlayerName(source);
    const identifiers = client.utils.getPlayerIdentifiers(source);
    
    return interaction.reply({
      content: `**${targetUser.displayName}** is online as **${playerName}** (ID: ${source})`,
      ephemeral: true,
    });
  },
};
```

### Reply Options

```javascript
// Simple text reply
interaction.reply({ content: "Hello!" });

// Ephemeral reply (only visible to user)
interaction.reply({ 
  content: "This is private",
  ephemeral: true 
});

// Reply with embed
interaction.reply({ embeds: [embed] });

// Reply with buttons
const { MessageActionRow, MessageButton } = require("discord.js");
const row = new MessageActionRow().addComponents(
  new MessageButton()
    .setCustomId("button_id")
    .setLabel("Click Me")
    .setStyle("PRIMARY"),
);
interaction.reply({ content: "Pick one:", components: [row] });

// Defer reply (for long operations)
await interaction.deferReply();
// Do work...
await interaction.editReply({ content: "Done!" });

// Follow up after initial reply
await interaction.reply({ content: "Starting..." });
// Do work...
await interaction.followUp({ content: "Finished!" });
```

### Best Practices

1. **Always validate input** - Check if player IDs are valid before using them
2. **Use ephemeral responses for errors** - Don't spam the channel with error messages
3. **Log important actions** - Use `client.utils.log` for audit trails
4. **Handle errors gracefully** - Use try/catch and provide helpful error messages
5. **Use defer for long operations** - If command takes >3 seconds, defer the reply
6. **Check permissions** - The role system handles this automatically, but double-check for sensitive operations

---

## Creating New Events

Events respond to Discord.js events. Each event is a file in `server/events/`.

### Event Structure

Create `server/events/yourevent.js`:

```javascript
module.exports = {
  // Required: Event name (must match discord.js event name)
  name: "messageCreate",
  
  // Optional: Run only once (default: false)
  once: false,
  
  // Required: Event handler function
  run: async (client, ...args) => {
    // For messageCreate event, args[0] is the message object
    const message = args[0];
    
    // Your event logic here
    if (message.content.startsWith("!ping")) {
      message.reply("Pong!");
    }
  },
};
```

### Common Discord.js Events

| Event Name | When it Fires | Arguments |
|------------|---------------|-----------|
| `ready` | Bot successfully connects | None |
| `messageCreate` | Message sent in Discord | `message` |
| `messageDelete` | Message deleted | `message` |
| `messageUpdate` | Message edited | `oldMessage`, `newMessage` |
| `interactionCreate` | Slash command or button pressed | `interaction` |
| `guildMemberAdd` | User joins Discord server | `member` |
| `guildMemberRemove` | User leaves Discord server | `member` |
| `guildMemberUpdate` | Member roles/nickname changes | `oldMember`, `newMember` |
| `guildBanAdd` | User banned from Discord | `ban` |
| `guildBanRemove` | User unbanned from Discord | `ban` |

### Example: Staff Chat Forwarding Event

```javascript
module.exports = {
  name: "messageCreate",
  run: async (client, message) => {
    // Ignore bot messages
    if (message.author.bot) return;
    
    // Check if message is in staff channel
    if (message.channel.id !== client.config.DiscordStaffChannelId) return;
    
    // Check if staff chat forwarding is enabled
    if (!client.config.EnableStaffChatForwarding) return;
    
    // Send message to all in-game staff
    const content = `${message.author.username}: ${message.content}`;
    client.utils.sendStaffChatMessage(client, "Discord", content);
    
    // Log the message
    client.utils.log.info(`[Staff Chat] ${content}`);
  },
};
```

### Example: Auto-Role on Join

```javascript
module.exports = {
  name: "guildMemberAdd",
  run: async (client, member) => {
    // Give new members a default role
    const defaultRoleId = "ROLE_ID_HERE";
    
    try {
      await member.roles.add(defaultRoleId);
      client.utils.log.info(`Gave default role to ${member.displayName}`);
    } catch (error) {
      client.utils.log.error(`Failed to give role to ${member.displayName}: ${error}`);
    }
  },
};
```

### Example: Monitor Role Changes

```javascript
module.exports = {
  name: "guildMemberUpdate",
  run: async (client, oldMember, newMember) => {
    // Check if roles changed
    const oldRoles = oldMember.roles.cache;
    const newRoles = newMember.roles.cache;
    
    // Find added roles
    const addedRoles = newRoles.filter(role => !oldRoles.has(role.id));
    
    // Find removed roles
    const removedRoles = oldRoles.filter(role => !newRoles.has(role.id));
    
    if (addedRoles.size > 0) {
      client.utils.log.info(
        `${newMember.displayName} gained roles: ${addedRoles.map(r => r.name).join(", ")}`
      );
    }
    
    if (removedRoles.size > 0) {
      client.utils.log.info(
        `${newMember.displayName} lost roles: ${removedRoles.map(r => r.name).join(", ")}`
      );
    }
  },
};
```

---

## Creating Addons

Addons are powerful extensions that can add entirely new functionality. They're classes instantiated at server startup.

### Addon Structure

Create `server/addons/youraddon.js`:

```javascript
class YourAddon {
  constructor(cas) {
    // Store cas object for accessing bot, config, utils, etc.
    this.cas = cas;
    this.bot = cas.bot;
    this.config = cas.config;
    this.utils = cas.utils;
    
    // Initialize your addon
    this.init();
  }
  
  init() {
    // Setup code here
    this.utils.log.info("YourAddon initialized!");
    
    // Register events, exports, etc.
    this.registerEvents();
  }
  
  registerEvents() {
    // Register RedM events
    on("playerJoining", (oldId) => {
      const source = global.source;
      this.onPlayerJoin(source);
    });
  }
  
  onPlayerJoin(source) {
    // Handle player joining
    const name = GetPlayerName(source);
    this.utils.log.info(`${name} joined the server`);
  }
  
  // Your addon methods
  myAddonMethod(param) {
    return `Addon processed: ${param}`;
  }
}

module.exports = YourAddon;
```

### Accessing Addon from Other Scripts

Once created, your addon is accessible at `cas.youraddon`:

```javascript
// In a command file
run: async (client, interaction, args) => {
  // Access your addon
  const result = client.cas.youraddon.myAddonMethod("test");
  return interaction.reply({ content: result });
}
```

### Example: Logging Addon

The built-in `log.js` addon provides webhook logging:

```javascript
class Log {
  constructor(cas) {
    this.cas = cas;
    this.config = cas.config;
    this.webhooks = cas.config.LoggingWebhooks;
    
    // Register export for other resources
    global.exports("log", (category, data) => {
      return this.send(category, data);
    });
  }
  
  send(category, data) {
    if (!this.config.EnableLoggingWebhooks) return false;
    if (!this.webhooks[category]) {
      this.cas.utils.log.warn(`No webhook configured for category: ${category}`);
      return false;
    }
    
    const webhookUrl = this.webhooks[category];
    
    // Format data as embed
    const embed = {
      title: data.title || "Server Log",
      description: data.message || "",
      color: data.color || 0x0099FF,
      fields: data.fields || [],
      timestamp: new Date().toISOString(),
    };
    
    // Send to webhook
    PerformHttpRequest(webhookUrl, (status, text, headers) => {
      if (status !== 204) {
        this.cas.utils.log.error(`Webhook failed for ${category}: ${status}`);
      }
    }, "POST", JSON.stringify({
      username: this.config.LoggingWebhookName,
      content: data.ping ? `<@${this.config.LoggingAlertPingId}>` : null,
      embeds: [embed],
    }), {
      ["Content-Type"] = "application/json",
    });
    
    return true;
  }
}

module.exports = Log;
```

**Usage from other resources:**

```lua
-- From another RedM resource
exports['cas_adminbot']:log("admin", {
  title = "Player Banned",
  message = "A player was banned from the server",
  fields = {
    { name = "Player", value = playerName, inline = true },
    { name = "Reason", value = reason, inline = true },
    { name = "Admin", value = adminName, inline = true },
  },
  color = 16711680, -- Red
  ping = true,
})
```

---

## Exports and Integrations

CAS AdminBot exposes several exports for use by other resources.

### Available Exports

#### `isRolePresent(identifier, role)`

Check if a player has a Discord role.

```lua
-- Lua example
local source = source
local hasAdminRole = exports['cas_adminbot']:isRolePresent(source, "ROLE_ID_HERE")

if hasAdminRole then
  print("Player has admin role!")
end
```

```javascript
// JavaScript example
const hasRole = global.exports.cas_adminbot.isRolePresent(source, "ROLE_ID");
```

**Parameters:**
- `identifier` - Player source, Discord ID, or member object
- `role` - Single role ID (string) or array of role IDs

**Returns:** `true` if player has the role, `false` otherwise

#### `getRoles(identifier)`

Get all Discord roles for a player.

```lua
local source = source
local roles = exports['cas_adminbot']:getRoles(source)

for _, roleId in ipairs(roles) do
  print("Player has role: " .. roleId)
end
```

**Returns:** Array of role IDs

#### `getName(identifier)`

Get Discord display name for a player.

```lua
local source = source
local discordName = exports['cas_adminbot']:getName(source)
print("Discord name: " .. discordName)
```

**Returns:** Discord display name or `false`

#### `getDiscordId(identifier)`

Get Discord ID from player source.

```lua
local source = source
local discordId = exports['cas_adminbot']:getDiscordId(source)

if discordId then
  print("Discord ID: " .. discordId)
end
```

**Returns:** Discord ID (string) or `false`

### Integration Examples

#### Lock Vehicle to Discord Role

```lua
RegisterNetEvent('lockVehicleCheck')
AddEventHandler('lockVehicleCheck', function(vehicleId)
  local source = source
  local hasVIPRole = exports['cas_adminbot']:isRolePresent(source, "VIP_ROLE_ID")
  
  if hasVIPRole then
    -- Allow access
    TriggerClientEvent('unlockVehicle', source, vehicleId)
  else
    -- Deny access
    TriggerClientEvent('chat:addMessage', source, {
      args = {"System", "You need VIP role to use this vehicle!"}
    })
  end
end)
```

#### Whitelist Check on Resource

```lua
AddEventHandler('playerConnecting', function(name, setKickReason, deferrals)
  local source = source
  
  deferrals.defer()
  
  Wait(100)
  
  local hasWhitelist = exports['cas_adminbot']:isRolePresent(source, {
    "WHITELIST_ROLE_1",
    "WHITELIST_ROLE_2",
  })
  
  if hasWhitelist then
    deferrals.done()
  else
    deferrals.done("You are not whitelisted!")
  end
end)
```

#### Dynamic Permissions Based on Roles

```lua
AddEventHandler('playerJoining', function()
  local source = source
  
  Wait(2000) -- Wait for identifiers to load
  
  if exports['cas_adminbot']:isRolePresent(source, "ADMIN_ROLE_ID") then
    ExecuteCommand('add_principal player.' .. source .. ' group.admin')
  end
  
  if exports['cas_adminbot']:isRolePresent(source, "MODERATOR_ROLE_ID") then
    ExecuteCommand('add_principal player.' .. source .. ' group.moderator')
  end
  
  if exports['cas_adminbot']:isRolePresent(source, "VIP_ROLE_ID") then
    ExecuteCommand('add_principal player.' .. source .. ' group.vip')
  end
end)
```

---

## Logging System

The bot includes a comprehensive logging utility accessible via `client.utils.log`.

### Log Methods

```javascript
// Info log (cyan color)
client.utils.log.info("Information message");

// Warning log (yellow color)
client.utils.log.warn("Warning message");

// Error log (red color)
client.utils.log.error("Error message");

// Generic log (white color)
client.utils.log.log("Generic message");

// Custom log with your own tag and color
client.utils.log.write("Custom message", {
  color: "\x1b[1;35m", // Magenta
  tag: "CUSTOM",
  error: false, // false = stdout, true = stderr
});
```

### Log Format

```
[cas_adminbot][2025-10-25 12:30:45][TAG]: Message
```

### Cleaning Objects for Logging

```javascript
const obj = { key: "value", nested: { data: 123 } };
const cleanedString = client.utils.log.clean(obj);
client.utils.log.info(cleanedString);
```

### Example Command with Logging

```javascript
run: async (client, interaction, args) => {
  const playerId = args.id;
  const playerName = GetPlayerName(playerId);
  
  if (!playerName) {
    client.utils.log.warn(
      `[${interaction.member.displayName}] Attempted to kick invalid ID: ${playerId}`
    );
    return interaction.reply({
      content: "Invalid player ID.",
      ephemeral: true,
    });
  }
  
  DropPlayer(playerId, "Kicked by admin");
  
  client.utils.log.info(
    `[${interaction.member.displayName}] Kicked ${playerName} (${playerId})`
  );
  
  return interaction.reply({
    content: `Successfully kicked ${playerName}.`,
  });
}
```

---

## Troubleshooting

### Bot Won't Start

**Error:** `[TOKEN_INVALID]`
- **Solution:** Your bot token is invalid. Generate a new token in Discord Developer Portal and update `config.js`.

**Error:** `[DISALLOWED_INTENTS]`
- **Solution:** You didn't enable the required intents. Go to Discord Developer Portal → Your App → Bot → Enable both **SERVER MEMBERS INTENT** and **MESSAGE CONTENT INTENT**.

**Error:** `DiscordGuildId was not found`
- **Solution:** Double-check your Discord server ID in `config.js`. Right-click your server icon → Copy ID (you must have Developer Mode enabled in Discord).

**Error:** `Missing Access` when registering commands
- **Solution:** Re-invite the bot using the invite URL with the `applications.commands` scope.

### Bot is Online but Commands Don't Appear

1. **Check if slash commands are enabled:**
   ```javascript
   const EnableDiscordSlashCommands = true;
   ```

2. **Re-invite the bot** with the correct scope:
   ```
   https://discord.com/api/oauth2/authorize?client_id=YOUR_BOT_ID&permissions=8&scope=bot%20applications.commands
   ```

3. **Wait a few minutes** - Discord can take time to register slash commands globally

4. **Check for command errors** in server console - malformed commands won't register

### Commands Are Registered But Don't Work

1. **Check role permissions** - Make sure your Discord role IDs are correct in `config.js`
2. **Check bot permissions** - Ensure bot has Administrator permission in Discord
3. **Check console logs** - Look for errors when command is executed
4. **Try restarting the resource:** `restart cas_adminbot`

### Whitelist Not Working

1. **Verify whitelist is enabled:**
   ```javascript
   const EnableWhitelistChecking = true;
   ```

2. **Check role IDs** - Make sure `DiscordWhitelistRoleIds` contains the correct role IDs

3. **Ensure players have Discord open** - RedM must be able to detect their Discord ID

4. **Check for errors** in console when player connects

### Staff Chat Not Working

1. **Verify staff chat is enabled:**
   ```javascript
   const EnableStaffChatForwarding = true;
   ```

2. **Check staff channel ID** - Make sure `DiscordStaffChannelId` is correct

3. **Verify bot permissions** - Bot needs Send Messages permission in the staff channel

4. **Check role IDs** - Staff roles must be configured correctly

### VorpCore Commands Don't Appear

1. **Ensure VorpCore is installed and running**
2. **Check load order** - VorpCore must be loaded **before** cas_adminbot in `server.cfg`:
   ```cfg
   ensure vorp_core
   ensure cas_adminbot
   ```
3. **Check console** - Look for "VorpCore found!" message

### High CPU Usage / Lag

1. **Disable debug logs:**
   ```javascript
   const DebugLogs = false;
   ```

2. **Reduce bot status update frequency** - Edit `server/events/ready.js` to increase interval

3. **Check for command loops** - Make sure custom commands don't have infinite loops

### Discord ID Not Detected

1. **Player must have Discord open** before starting RedM
2. **Restart both Discord and RedM** if issue persists
3. **Check Discord is running in background** (not just web version)

---

## Advanced Usage

### Custom Localization

Edit or create new locale files in `locales/` directory:

```javascript
// locales/es.js (Spanish)
const locale = {
  checkingWhitelist: "Hola {name}, estamos verificando tu estado de whitelist...",
  discordNotOpen: "Debes tener Discord abierto antes de iniciar RedM",
  notInDiscordServer: "No estás en el servidor de Discord de {servername}",
  notWhitelisted: "No tienes el rol requerido para unirte a este servidor",
  kickedWithoutReason: "Fuiste expulsado por el personal",
  announcement: "ANUNCIO",
  directMessage: "PERSONAL",
  staffchat: "CHAT DE PERSONAL",
};

module.exports = locale;
```

Set in `config.js`:
```javascript
const LanguageLocaleCode = "es";
```

### Dynamic Bot Status

Edit `server/events/ready.js` to customize how bot status updates:

```javascript
// Cycle through statuses every 30 seconds
if (client.config.EnableBotStatusMessages) {
  let index = 0;
  setInterval(() => {
    const status = client.config.BotStatusMessages[index];
    const formatted = client.utils.replaceGlobals(client.cas, status);
    
    client.user.setActivity(formatted, { type: "WATCHING" });
    
    index = (index + 1) % client.config.BotStatusMessages.length;
  }, 30000);
}
```

**Activity Types:**
- `PLAYING` - Playing {status}
- `WATCHING` - Watching {status}
- `LISTENING` - Listening to {status}
- `STREAMING` - Streaming {status}
- `COMPETING` - Competing in {status}

### Pagination for Large Data

Use the built-in pagination system:

```javascript
const { MessageButton } = require("discord.js");

const pages = [
  new client.Embed().setTitle("Page 1").setDescription("Content 1"),
  new client.Embed().setTitle("Page 2").setDescription("Content 2"),
  new client.Embed().setTitle("Page 3").setDescription("Content 3"),
];

const buttons = [
  new MessageButton()
    .setCustomId("previous")
    .setLabel("◀")
    .setStyle("PRIMARY"),
  new MessageButton()
    .setCustomId("next")
    .setLabel("▶")
    .setStyle("PRIMARY"),
];

await client.paginationEmbed(interaction, pages, buttons, 120000);
```

### Custom Embed Builder

```javascript
const embed = new client.Embed()
  .setTitle("Server Statistics")
  .setDescription("Current server information")
  .setColor(0x3498DB) // Hex color
  .setThumbnail("https://example.com/image.png")
  .setImage("https://example.com/banner.png")
  .addField("Online Players", `${GetNumPlayerIndices()}`, true)
  .addField("Server Name", client.config.RedMServerName, true)
  .addField("Uptime", "24 hours", true)
  .setFooter({ 
    text: "Requested by " + interaction.member.displayName,
    iconURL: interaction.user.displayAvatarURL(),
  })
  .setTimestamp()
  .setAuthor({ 
    name: "CAS AdminBot",
    iconURL: client.user.displayAvatarURL(),
  });

interaction.reply({ embeds: [embed] });
```

### Secure Configuration with Environment Variables

Instead of hardcoding tokens in `config.js`, use convars in `server.cfg`:

```cfg
set discord_token "your_token_here"
set discord_guild_id "your_guild_id"

ensure cas_adminbot
```

### Database Integration Example

If you want to track command usage in a database:

```javascript
// In your command file
run: async (client, interaction, args) => {
  // Your command logic
  const playerId = args.id;
  DropPlayer(playerId, "Kicked");
  
  // Log to database
  MySQL.Async.execute(
    'INSERT INTO command_logs (admin_discord_id, command, target, timestamp) VALUES (@admin, @command, @target, NOW())',
    {
      ['@admin']: interaction.user.id,
      ['@command']: 'kick',
      ['@target']: playerId,
    }
  );
  
  return interaction.reply({ content: "Player kicked!" });
}
```

### Webhooks for External Monitoring

Send important events to external services:

```javascript
// In an event or command
const webhookUrl = "https://your-monitoring-service.com/webhook";
const data = {
  event: "player_banned",
  player: playerName,
  admin: interaction.user.username,
  timestamp: Date.now(),
};

PerformHttpRequest(webhookUrl, (status) => {
  client.utils.log.info(`Webhook sent: ${status}`);
}, "POST", JSON.stringify(data), {
  ["Content-Type"] = "application/json"
});
```

### Rate Limiting Commands

Prevent command spam:

```javascript
const cooldowns = new Map();

module.exports = {
  name: "expensive_command",
  description: "A command with cooldown",
  role: "user",
  
  run: async (client, interaction, args) => {
    const userId = interaction.user.id;
    const cooldownTime = 60000; // 60 seconds
    
    if (cooldowns.has(userId)) {
      const expirationTime = cooldowns.get(userId) + cooldownTime;
      const timeLeft = expirationTime - Date.now();
      
      if (timeLeft > 0) {
        return interaction.reply({
          content: `Please wait ${Math.ceil(timeLeft / 1000)} more seconds.`,
          ephemeral: true,
        });
      }
    }
    
    cooldowns.set(userId, Date.now());
    setTimeout(() => cooldowns.delete(userId), cooldownTime);
    
    // Execute command
    return interaction.reply({ content: "Command executed!" });
  },
};
```

---

## Support & Contributing

### Getting Help

- **Discord Support**: Join the [official Discord](https://discord.gg/QxjVKBEqXX)
- **Documentation**: This file and `readme.md`
- **Common Issues**: Check [Troubleshooting](#troubleshooting) section

### Reporting Bugs

When reporting bugs, include:
1. RedM artifacts version
2. Bot version
3. Complete error message from console
4. Steps to reproduce
5. Your configuration (redact tokens/IDs)

### Feature Requests

Have an idea? Open a ticket on Discord with:
- Description of the feature
- Use case / why it's needed
- Example of how it would work

---

## License

© 2025 by Brownyprod

This software (**cas_adminbot**) is licensed exclusively to the client.

**Restrictions:**
- Reselling, redistribution, or sharing with any third party is strictly prohibited
- The client is the sole authorized user of this software
- Unauthorized use, copying, or distribution may result in legal action

If you want to use or modify this software for your own RedM server, please contact the author for a proper license.

---

## Credits

- **Author**: brownyprod
- **Framework**: discord.js (custom fork)
- **Store**: https://cas.tebex.io


---

**End of Documentation**

*For the latest updates and announcements, join our Discord community at https://discord.gg/QxjVKBEqXX*
