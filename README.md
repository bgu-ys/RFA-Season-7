# RFA SEASON 7 Development Roadmap

## **Infrastructure**

### *1. Signings*

A major focus for the upcoming season revolves around updating the infrastructure that the league runs under. One of the biggest changes comes through **Discord integration**.

Managers will, like in previous seasons, sign players directly using the `/sign` application command through the **RFA Bot**.

When a manager runs the `/sign` command in Discord, the interaction will first go through the Discord bot using **Discord.js**, then parse the command data, extracting the tagged user. At this point, the bot queries **Rover** or **Bloxlink’s API** to retrieve the linked **Roblox UserId** for that Discord account.

Once staff review the signing in the staff channel, they’ll be given an interactive panel to either accept or decline the request. If they choose to accept, they’ll also need to select the **player’s class** that determines where the player belongs in the spreadsheet.

These classes are arranged in **separate sections of the Google Sheet**, so the bot needs to insert the player into the correct section rather than simply appending to the bottom. Instead of a plain append, we'll account for this by making it so that the bot first looks up the range of rows allocated to the chosen class.

For example, if a staff member selects **“B Class”** from the accept panel, the bot queries the sheet for the section labeled *B Class*, finds the next available empty row in that section, and inserts the new entry there.

This ensures that new players are always stored in the correct grouping, and prevents data from overlapping across tiers. The final request payload sent to Google Sheets includes the **Roblox UserId, Discord handle, team name, and the staff-selected class**, all written into the appropriate class section of the sheet.

The bot then posts a confirmation back into Discord once the operation succeeds.

---

### *2. Scoresheets*

The plan is to introduce **automated scoresheets**. Currently, officials fill these out by hand after games, which has proven to be time-consuming and do not end up being posted punctually.

In the new system, a UI will be available in-game for officials. All players on *"Home"*, *"Away"*, *"-Home GK"*, and *"-Away GK"* are listed, and the official only needs to click a player’s name, assign a minute, and log the goal. At the end of the match, the scoresheet can be sent directly to the server with one click.

To achieve this, the RFA system will use **Roblox’s HTTPService** to post the formatted scoresheet through a **Discord webhook**, meaning the official match report appears instantly in the league’s designated channel.

---

### *3. Player Verification*

To prevent ineligible players from taking part in games, officials will be able to run a **verification command** in-game.

This pulls the current player list, matches each username/ID against the **Google Sheets roster**, and checks their registered team. An interface on the official’s screen then displays which team each player is cleared to play for.

This cross-checking is again made possible with **Roblox–Google Sheets API integration**.

---

## **Tool Modularization**

Across most leagues, the tool setup and structure is developed more or less the same. Currently, each player has each tool copied to their player, using separate LocalScripts to tie the keybinds.

This setup is serviceable, but it creates significant overhead when hundreds of scripts are running simultaneously across multiple players.

The new approach consolidates this logic into a single **ModuleScript**. Instead of having multiple LocalScripts inside each tool, the tools now reference the same centralized module for input handling and execution.

This modular structure reduces redundancy, lowers the memory footprint, and improves performance on both the client and server. More importantly, it allows for faster debugging and future updates as changes will only need to be made once in the module, rather than across 40+ scripts.

The end result is a leaner, more efficient tool system. In a more efficient environment, we can hope to mitigate problems related to reach and the react system.

The introduction of a modularized toolset will ensure RFA owns its custom tools and practically eliminate any threat of a blacklist by another league.

---

## **Cosmetics & Marketability**

In order for RFA to stand out, the presentation side of the league must be focused upon. As RFA has been doing, many leagues run the exact same structure and can seem copy-paste to players.

Most leagues are only distinguishable by their unique toolbases. We plan to put effort into features that enhance immersion and help market the league to new audiences.

### 1. Cinematics

Upon goal detection, officials receive a prompt to confirm or deny the goal. Once confirmed, a celebration cutscene begins, checking what a player's equipped celebration is, then playing the scene for all users in the server to see.

Pre-match walkout cinematics can also be included. A single command run by an official will trigger an automated cutscene lasting around 30 seconds, covering both teams as they line up and enter the pitch.

This adds to the broadcast feel of league matches and raises the overall standard of the league, feeling much more organized and memorable.

On-screen graphics should also be a major point of interest. The score line should show directly before every kick-off, as well as graphics for things like yellow cards and disallowed goals.

### 2. Economy

With this, a possible, but not guaranteed addition is an **economy system tied to cosmetics**.

Players will accumulate currency based on playtime in a dedicated hub game. In the hub, players can open packs to unlock new goal celebrations, ball-hold animations, and other cosmetic items. Ownership data will be stored using **DataStores** and replicated across the hub and match servers.

This way, when a player equips a cosmetic in the hub, it carries over seamlessly into the match pitch.

Beyond player engagement, this system also lays the groundwork for **monetization**, giving the league a sustainable way to fund staff and development.
