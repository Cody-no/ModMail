
# ModMail
A modmail bot for Discord.

A fork of [Tobiwan's modmail](https://github.com/TobiWan54/ModMail)

Credit goes to Tobiwan for making the bot. I'm just modifying it to match some feature that [ModMail by Chamburr ](https://github.com/chamburr/modmail) has.

## Features
When a user messages the bot, a new thread is created inside your ModMail forum channel, representing a ticket. As you would expect from a Modmail bot,
all the messages in the ticket are logged when the ticket is closed. You can also blacklist users for misuse, and set pre-defined snippets which can
be sent with a single command.

However, this bot has a few additional features that make it unique:

- **Forum-based tickets** keep every conversation inside a single thread so moderators can collaborate without juggling extra channels.
From v.1.1.0 onwards there is also an option to send messages only with the commands `!reply` and `!areply` (anonymous).

- `!search` allows you to retrieve the logs of a user's previous tickets, and to search for specific phrases within them.

- `!send` creates a new ticket and sends an anonymous message to a user that does not already have a ticket open.

- `!sendmany`, the various `!replymany` flavours, and the `!closemany` family let you tag a group of tickets with a temporary label, deliver the same update to each one, and close them together when the follow-up is finished.

### Group tag bulk commands

Use the group tag commands when you need to contact several members about the same topic. Every command works with the temporary group tag created by `!sendmany` so you can keep their ticket updates together.

- `!sendmany <ids> <group name> <message>` — Provide a comma-separated list of user IDs and a group name. The command opens tickets as needed, sends the anonymous message (or attachments) to every member, and tags their threads so you can follow up later.
- `!replymany <group name> <message>` — Sends a normal moderator reply to each ticket currently tagged with the supplied group name. You can include attachments exactly like a regular reply.
- `!areplymany <group name> <message>` — Delivers the same update anonymously so the acting moderator stays hidden inside every ticket thread.
- `!replytmany <group name> <language> <message>` and `!areplytmany <group name> <language> <message>` — Translate the shared response before sending it, with the anonymous variant keeping moderator identities concealed.
- `!closemany <group name> [reason]` — Closes all tickets tagged with the group name and removes the temporary tag afterwards. The optional reason is logged just like the standard `!close` command.
- `!aclosemany <group name> [reason]` — Closes the tickets while anonymising the moderator in the internal log entry.
- `!clostmany <group name> <language> [reason]` (alias `!closetmany`) and `!aclosetmany <group name> <language> [reason]` — Close every tagged ticket after translating the reason for users, with the anonymous version keeping moderator details private.
When you finish working with a group, the closing commands automatically delete the temporary forum tag once every ticket has been removed.

- `!replyt`+`!areplyt` reply to the ticket with a translated version of your message. You have to include the language you wish to translate to in the command. The original message is sent
in the case that the translation sounds off and the receiver wants to self verify
 
- `!closet` close the ticket with a translated version of your message. Same as replyt/areplyt

- **Ai Summaries** have been added to the ticket closed log so other moderators can get an idea of what happened in the ticket at a glance. This is a feature inspired by Chamburr's Modmail

- **Forum counter** automatically updates the forum channel name with the number of open tickets so moderators instantly know how busy the inbox is.

Once you have the bot running, the `!help` command will show you a list of all the available commands and their sub-commands.

## Setup

To use this bot, you will have to create a bot account for it on the [Discord Developer Portal](https://discord.com/developers)
and host the script yourself. Oracle Cloud and Google Cloud both have free tiers that provide sufficiently-resourced instances 
(virtual machines) for hosting.

The script requires Python 3.10 or higher and the packages listed under Dependancies.

### Configuration
Fill out [config.json](templates/config.json) with your own values, and put it in the same
directory as [modmail.py](modmail.py). Then run the script, and your bot will be online!
Create a `modmail.env` file alongside the script and store your `DISCORD_TOKEN` and
`OPENAI_API_KEY` inside it. These are loaded automatically at runtime.

Snippets are stored in `snippets.json`, the blacklist is stored in `blacklist.json` and ticket logs are indexed in the SQLite database `logs.db`.
Along with `counter.txt` these are automatically created by the script, so do not delete them.

I would recommend storing your own external backups, especially of `logs.db` because this index cannot be recovered if lost.

#### config.json

- `token` is your bot account's token from the Discord Developer Portal. This value can be set in a `modmail.env` file.
- `OPENAI_API_KEY` should also be placed in `modmail.env` if you use the AI features.
- `guild_id` is your server's ID.
- `category_id` keeps the legacy category identifier available for integrations that still expect it. Set it to the category that previously held individual ticket channels (or keep it aligned with your forum's category).
- `forum_channel_id` is the ID of the forum channel where ticket threads should be created. You will have to create this yourself.
- `log_channel_id` is the ID of the channel that ticket logs will be sent in.
This must remain a regular text channel; do not point it at the forum itself.
You will have to create this yourself.
- `error_channel_id` is the ID of the channel that you want error messages to be sent in.
This can be the log channel if you want, just set it to the same as above.
- `helper_role_id` is the ID of your server's helper or trainee role, which can use everything except the blacklist.
If you do not have a helper role, set this to the same value as below.
- `mod_role_id` is the ID of your server's moderator role, which can use all features.
- `bot_owner_id` is the ID of the user that error tracebacks will be DM'd to. Access to the `!eval` command for arbitrary code execution 
is given to the bot owner(s) in the Discord Developer Portal (although they will likely be the same user).
- `prefix` is the bot's prefix.
- `open_message` is the text that users will receive under "Ticket Created" when they open a ticket.
- `close_message` is the text that users will receive under "Ticket Closed" when a mod closes their ticket.
- Set `anonymous_tickets` to true to name ticket channels anonymously instead of using the user's name.
- `send_with_command_only` (true/false) only allows messages to be sent using `!reply` and `!areply`

The `!refresh` command will re-read the config file, so you can change these values without restarting the bot.
It also resets a few things behind the scenes which may help fix some issues.

### Dependancies

The required/working versions of these packages are listed in [requirements.txt](requirements.txt). To install them, simply use `pip install -r requirements.txt`

[bleach](https://github.com/mozilla/bleach)

[discord.py](https://github.com/Rapptz/discord.py)

[aiohttp](https://github.com/aio-libs/aiohttp) - async HTTP client used for log searches (installed with discord.py)

[openai](https://github.com/openai/openai-python) - used for GPT-4o ticket summaries and message translation

[python-dotenv](https://github.com/theskumar/python-dotenv) - loads environment variables from a `modmail.env` file
