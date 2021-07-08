# tmi-emote-parse
Load and parse Twitch, BTTV and FFZ emotes and badges from messages for multiple channels.

⚠ This module is mainly designed to be integrated in a https://github.com/tmijs/tmi.js environment but can also be used as a standalone with limited features.

## Table of Contents

- [Installation](#installation)
- [Usage](#usage)
  - [Without tmi.js](#without-tmijs)
  - [With tmi.js](#with-tmijs)
- [Documentation](#documentation)
  - [emoteParser.loadAssets()](#emoteparserloadassets)
  - [emoteParser.getLoaded()](#emoteparsergetloaded)
  - [emoteParser.getAllBadges()](#emoteparsergetallbadges)
  - [emoteParser.getAllEmotes()](#emoteparsergetallemotes)
  - [emoteParser.getBades()](#emoteparsergetbadges)
  - [emoteParser.replaceEmotes()](#emoteparserreplaceemotes)

## Installation
Install using npm:
```bash
npm install tmi-emote-parse
```

## Usage
### Without tmi.js
```js
// 🟦 Require the Module
const emoteParser = require("tmi-emote-parse");

// 🟦 Load emotes and badges for a specific channel to later use
emoteParser.loadAssets("twitch");
emoteParser.loadAssets("twitchdev");

emoteParser.events.on("emotes", (event) => {

    // Get all BTTV & FFZ Emotes used on a channel
    console.log(emoteParser.getAllEmotes(event.channel));
    /* 
        [{
          name: 'KEKW',
          type: 'ffz',
          img: 'https://cdn.frankerfacez.com/emote/381875/4'
        }, ...] 
    */
})

emoteParser.events.on("badges", (event) => {

    // Get all Badges available on a channel
    console.log(emoteParser.getAllBadges(event.channel));
    /* 
        [{
          name: 'bits/1000',
          info: 'cheer 1000',
          img: 'https://static-cdn.jtvnw.net/badges/v1/0d85a29e-79ad-4c63-a285-3acd2c66f2ba/3'
        }, ...] 
    */
})
```

### With tmi.js
```js
// 🟦 Require the Module
const emoteParser = require("tmi-emote-parse");

// 🅾 The following part is the tmi.js integration
// (Documentation can be found here: https://github.com/tmijs/tmi.js)
const tmi = require("tmi.js");

client = new tmi.Client({
    options: {
        debug: false
    },
    connection: {
        reconnect: true,
        secure: true
    },
    identity: {
        username: /* Channel Bot Username */,
        password: /* Channel Bot OAuth */
    },
    channels: [ '#twitch', '#twitchdev' ] /* Channels to join with leading '#' */
});
client.connect().catch(console.error);

// 🅾 tmi.js message event handler (as of tmi.js v1.4.2)
client.on('message', (channel, userstate, message, self) => {

    // 🟦 Use the tmi-emote-parse module here
    // Replace Emotes with HTML in a given message for a specific channel
    console.log(emoteParser.replaceEmotes(channel, userstate, message, self));
    /* 
        -> message: 'I can see you ariW' 
        -> output:  'I can see you <img class="message-emote" src="https://cdn.betterttv.net/emote/56fa09f18eff3b595e93ac26/3x"/>'
    */
    
    // Return the badges the message author uses on a specific channel
    console.log(emoteParser.getBadges(userstate, channel));
    /* 
        [{
          name: 'premium/1',
          info: 'Prime Gaming',
          img: 'https://static-cdn.jtvnw.net/badges/v1/bbbe0db0-a598-423e-86d0-f9fb98ca1933/3'
        }, ...] 
    */
});

// 🟦 Load emotes and badges for a specific channel to later parse/use
emoteParser.loadAssets("twitch");
emoteParser.loadAssets("twitchdev");
```

## Documentation
### emoteParser.loadAssets()
Load Emotes and Badges of a specific Twitch channel. _(Void)_

**Parameters:**
- `channel`: _String_ - Channel name
```js
emoteParser.loadAssets("twitch");
```

### emoteParser.getLoaded()
Check the loaded status of all or a specific channel. _(Object)_

**Parameters:**
- `channel`: _String_ - Channel name (optional)
```js
console.log(emoteParser.getLoaded("twitch"));
```
Returns something like this:
```js
{
  "twitch": { channel: 'twitch', emotes: true, badges: true }
}
```

### emoteParser.getAllBadges()
Return all badges present in the chat for a specific channel. _(Array)_

**Parameters:**
- `channel`: _String_ - Channel name
```js
console.log(emoteParser.getAllBadges("twitch"));
```
Returns something like this:
```js
[{
  name: 'bits/1000',
  info: 'cheer 1000',
  img: 'https://static-cdn.jtvnw.net/badges/v1/0d85a29e-79ad-4c63-a285-3acd2c66f2ba/3'
}, ...]
```

### emoteParser.getAllEmotes()
Return all BTTV & FFZ emotes present in the chat for a specific channel. _(Array)_

**Parameters:**
- `channel`: _String_ - Channel name
```js
console.log(emoteParser.getAllEmotes("twitch"));
```
Returns something like this:
```js
[{
  name: 'ariW',
  type: 'bttv',
  img: 'https://cdn.betterttv.net/emote/56fa09f18eff3b595e93ac26/3x'
}, ...]
```

### emoteParser.getBadges()
⚠ tmi.js only: Return all badges a message author uses for a specific channel. _(Array)_

**Parameters:**
- `userstate`: _Object_ - Twitch userstate object (tmi.js)
  - `userstate["badges-raw"]`: _String_ - User badges
  - `...`
- `channel`: _String_ - Channel name
```js
console.log(emoteParser.getBadges(userstate, "twitch"));
```
Returns something like this:
```js
[{
  name: 'bits/1000',
  info: 'cheer 1000',
  img: 'https://static-cdn.jtvnw.net/badges/v1/0d85a29e-79ad-4c63-a285-3acd2c66f2ba/3'
}, ...]
```

### emoteParser.replaceEmotes()
⚠ tmi.js only: Parses all legacy Twitch, BTTV and FFZ emotes to HTML in the message for a specific channel. _(String)_

**Parameters:**
- `message`: _String_ - Chat message
- `userstate`: _Object_ - Twitch userstate object (tmi.js)
  - `userstate["emotes"]`: _Object_ - Used emotes in message
  - `...`
- `channel`: _String_ - Channel name
```js
console.log(emoteParser.replaceEmotes("I can see you ariW", userstate, "twitch"));
```
Returns something like this:
```js
'I can see you <img class="message-emote" src="https://cdn.betterttv.net/emote/56fa09f18eff3b595e93ac26/3x"/>'
```
