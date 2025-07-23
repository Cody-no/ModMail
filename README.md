
# ModMail
A modmail bot for Discord.

A fork of [Tobiwan's modmail](https://github.com/TobiWan54/ModMail)

Credit goes to Tobiwan for making the bot. I'm just modifying it to match some feature that [ModMail by Chamburr ](https://github.com/chamburr/modmail) has.

## Features
When a user messages the bot, a new channel is created in a category in your server, representing a ticket. As you would expect from a Modmail bot, 
all the messages in the ticket are logged when the ticket is closed. You can also blacklist users for misuse, and set pre-defined snippets which can 
be sent with a single command.

However, this bot has a few additional features that make it unique:

- **Discussion threads** are created automatically for each ticket and logged when the ticket is closed.
This allows mods to discuss freely, without the risk of accidentally sending a rude message to the user! From v.1.1.0 onwards there is also an
option to send messages only with the commands `!reply` and `!areply` (anonymous).

- `!search` allows you to retrieve the logs of a user's previous tickets, and to search for specific phrases within them.

- `!send` creates a new ticket and sends an anonymous message to a user that does not already have a ticket open.

- **The following is features I (Codyno) have added onto the existing bot**: 

- `!replyt`+`!areplyt` reply to the ticket with a translated version of your message. You have to include the language you wish to translate to in the command. The original message is sent
in the case that the translation sounds off and the receiver wants to self verify
 
- `!closet` close the ticket with a translated version of your message. Same as replyt/areplyt

- **Ai Summaries** have been added to the ticket closed log so other moderators can get an idea of what happened in the ticket at a glance. This is a feature inspired by Chamburr's Modmail

- **Channel counter** is automatically added to the category where the tickets are made. This allows moderators to know that the category is about fill up or not. 

Once you have the bot running, the `!help` command will show you a list of all the available commands and their sub-commands.

## Setup

To use this bot, you will have to create a bot account for it on the [Discord Developer Portal](https://discord.com/developers)
and host the script yourself. Oracle Cloud and Google Cloud both have free tiers that provide sufficiently-resourced instances 
(virtual machines) for hosting.

The script requires Python 3.10 or higher and the packages listed under Dependancies.

### Configuration
Fill out [config.json](templates/config.json) with your own values, and put it in the same 
directory as [modmail.py](modmail.py). Then run the script, and your bot will be online!

Snippets are stored in `snippets.json`, the blacklist is stored in `blacklist.json` and ticket logs are indexed in the SQLite database `logs.db`.
Along with `counter.txt` these are automatically created by the script, so do not delete them.

I would recommend storing your own external backups, especially of `logs.db` because this index cannot be recovered if lost.

#### config.json

- `token` is your bot account's token from the Discord Developer Portal. This value can be set in a `.env` file.
- `OPENAI_API_KEY` should also be placed in the `.env` file if you use the AI features.
- `guild_id` is your server's ID.
- `category_id` is the ID of the category that tickets to be created in. You will have to create this yourself.
- `log_channel_id` is the ID of the channel that ticket logs will be sent in.
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
- `anonymous_tickets` (true/false) names ticket channels anonymously, rather than using the name of the user.
- `send_with_command_only` (true/false) only allows messages to be sent using `!reply` and `!areply`

The `!reloadconfig` command will re-read the config file so you can change these values without restarting the bot.
Use `!refresh` to manually update the ticket category name if the channel count becomes incorrect.

### Dependancies

The required/working versions of these packages are listed in [requirements.txt](requirements.txt). To install them, simply use `pip install -r requirements.txt`

[bleach](https://github.com/mozilla/bleach)

[discord.py](https://github.com/Rapptz/discord.py)

[aiohttp](https://github.com/aio-libs/aiohttp) - async HTTP client used for log searches (installed with discord.py)

[openai](https://github.com/openai/openai-python) - used for GPT-4o ticket summaries and message translation
[python-dotenv](https://github.com/theskumar/python-dotenv) - loads environment variables from a .env file
