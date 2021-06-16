<!-- Generated by documentation.js. Update this documentation by updating the source code. -->

### Table of Contents

-   [Statcord][1]
    -   [post][2]
    -   [autopost][3]
    -   [postCommand][4]
        -   [Parameters][5]
    -   [registerCustomFieldHandler][6]
        -   [Parameters][7]
-   [ShardingClient][8]
    -   [registerCustomFieldHandler][9]
        -   [Parameters][10]
    -   [postCommand][11]
        -   [Parameters][12]
    -   [post][13]
        -   [Parameters][14]
-   [Examples][19]
    -   [Normal Usage][20]
    -   [Sharding Usage][21]

## Statcord

### post

Manual posting

Emits the **[post][28]** event

### autopost

Auto posting

Emits the **[autopost-start][29]** event

### postCommand

Post stats about a command

#### Parameters

-   `command_name` **[string][18]** The name of the command that was run
-   `author_id` **[string][18]** The id of the user that ran the command

### registerCustomFieldHandler

Register the function to get the values for posting

#### Parameters

-   `customFieldNumber` **(`1` \| `2`)** Whether the handler is for customField1 or customField2
-   `handler`  **[Normal Handler][23]**

Returns **([Error][17] | null)** 

## ShardingClient NOT FUNCTIONAL YET

### registerCustomFieldHandler

Register the function to get the values for posting

#### Parameters

-   `customFieldNumber` **(`1` \| `2`)** Whether the handler is for customField1 or customField2
-   `handler`  **[Sharding Handler][24]**

Returns **([Error][17] | null)** 

### postCommand

Post stats about a command

#### Parameters

-   `command_name` **[string][18]** The name of the command that was run
-   `author_id` **[string][18]** The id of the user that ran the command
-   `client` **any** The discord client this command is being posted for

### post

Post all current stats to statcord

Emits the **[post][28]** event

## Handlers

### Normal Handler

Asynchronous function

#### Parameters

- `client` **[Eris Client][25]** The client is passed to your function when getting the data

Returns **(Promise<[string][18]>)**

### Sharding Handler NOT FUNCTIONAL YET

Asynchronous function

#### Parameters

- `manager` **[Eris ShardingManager][26]** The manager is passed to your function when getting the data

Returns **(Promise<[string][18]>)**

# Events

## post event

"post" - Emitted whenever a post to the api takes place

### Parameters

status - **(false | [Error][17] | [string][18])**

## autopost-start event

"autopost-start" - Emitted when autopost is started

# Examples

## Normal Usage

```javascript
const Statcord = require("statcord-eris");
const Discord = require("eris");

const client = new Discord.Client();
// Create statcord client
const statcord = new Statcord.Client({
    key: "statcord.com-APIKEY",
    client,
    postCpuStatistics: false, /* Whether to post CPU statistics or not, defaults to true */
    postMemStatistics: false, /* Whether to post memory statistics or not, defaults to true */
    postNetworkStatistics: false /* Whether to post network statistics or not, defaults to true */
});

/* Register custom fields handlers (these are optional, you are not required to use this function)
 * These functions are automatically run when posting
*/

// Handler for custom value 1
statcord.registerCustomFieldHandler(1, async (client) => {
    // Get and return your data as a string
});

// Handler for custom value 2
statcord.registerCustomFieldHandler(2, async (client) => {
    // Get and return your data as a string
});

// Client prefix
const prefix = "cs!";

client.on("ready", async () => {
    console.log("ready");

    // Start auto posting
    statcord.autopost();
});


client.on("message", async (message) => {
    if (message.author.bot) return;
    if (message.channel.type !== "text") return;

    if (!message.content.startsWith(prefix)) return;

    let command = message.content.split(" ")[0].toLowerCase().substr(prefix.length);

    // Post command
    statcord.postCommand(command, message.author.id);

    if (command == "say") {
        message.channel.send("say");
    } else if (command == "help") {
        message.channel.send("help");
    } else if (command == "post") {
        // Only owner runs this command
        if (message.author.id !== "bot_owner_id") return;

        // Example of manual posting
        statcord.post();
    }
});

statcord.on("autopost-start", () => {
    // Emitted when statcord autopost starts
    console.log("Started autopost");
});

statcord.on("post", status => {
    // status = false if the post was successful
    // status = "Error message" or status = Error if there was an error
    if (!status) console.log("Successful post");
    else console.error(status);
});

client.login("TOKEN");
```

## Sharding Usage NOT FUNCTIONAL YET

#### **`sharder.js`**
```javascript
    const Discord = require("eris");
    const Statcord = require("statcord-eris");

    const manager = new Discord.ShardingManager('./bot.js', { token: "TOKEN"});
    // Create statcord sharding client
    const statcord = new Statcord.ShardingClient({
        key: "statcord.com-APIKEY",
        manager,
        postCpuStatistics: false, /* Whether to post CPU statistics or not, defaults to true */
        postMemStatistics: false, /* Whether to post memory statistics or not, defaults to true */
        postNetworkStatistics: false, /* Whether to post network statistics or not, defaults to true */
        autopost: false /* Whether to auto post or not, defaults to true */
    });

    /* Register custom fields handlers (these are optional, you are not required to use this function)
    * These functions are automatically run when posting
    */

    // Handler for custom value 1
    statcord.registerCustomFieldHandler(1, async (manager) => {
        // Get and return your data as a string
    });

    // Handler for custom value 2
    statcord.registerCustomFieldHandler(2, async (manager) => {
        // Get and return your data as a string
    });

    // Spawn shards, statcord works with both auto and a set amount of shards
    manager.spawn();

    // Normal shardCreate event
    manager.on("shardCreate", (shard) => {
        console.log(`Spawned shard ${shard.id}`);
    });

    statcord.on("autopost-start", () => {
        // Emitted when statcord autopost starts
        console.log("Started autopost");
    });

    statcord.on("post", status => {
        // status = false if the post was successful
        // status = "Error message" or status = Error if there was an error
        if (!status) console.log("successful post");
        else console.error(status);
    });
```

#### **`bot.js`**
```javascript
const Discord = require("eris");
const Statcord = require("statcord-eris");

const client = new Discord.Client();
/* There is no need to create a statcord client in the bot script,
because it has already been made in the sharding script
*/

// Client prefix
const prefix = "cs!";

client.on("ready", async () => {
    console.log("ready");
});

client.on("message", async (message) => {
    if (message.author.bot) return;
    if (message.channel.type !== "text") return;

    if (!message.content.startsWith(prefix)) return;

    let command = message.content.split(" ")[0].toLowerCase().substr(prefix.length);

    // Post command
    Statcord.ShardingClient.postCommand(command, message.author.id, client);

    if (command == "say") {
        message.channel.send("say");
    } else if (command == "help") {
        message.channel.send("help");
    } else if (command == "post") {
        // Only owner runs this command
        if (message.author.id !== "bot_owner_id") return;

        // Example of manual posting
        Statcord.ShardingClient.post(client);

        // Errors on the sharding client will be sent to the console straight away
    }
});

client.login("TOKEN");
```


## Contributing

Contributions are always welcome!\
Take a look at any existing issues on this repository for starting places to help contribute towards, or simply create your own new contribution to the project.

When you are ready, simply create a pull request for your contribution and we will review it whenever we can!

### Donating

You can also help me and the project out by sponsoring me through a [donation on PayPal](http://paypal.me/labdiscord).


<!-- Discussion & Support -->
## Discussion, Support and Issues

Need support with this project, have found an issue or want to chat with others about contributing to the project?
> Please check the project's issues page first for support & bugs!

Not found what you need here?

* If you have an issue, please create a GitHub issue here to report it, include as much detail as you can.
* _Alternatively,_ You can join our Discord server to discuss any issue or to get support for the project.:

<a href="http://statcord.com/discord" target="_blank">
    <img src="https://discordapp.com/api/guilds/608711879858192479/embed.png" alt="Discord" height="30">
</a>




[1]: #statcord

[2]: #post

[3]: #autopost

[4]: #postcommand

[5]: #parameters

[6]: #registercustomfieldhandler

[7]: #parameters-1

[8]: #shardingclient

[9]: #registercustomfieldhandler-1

[10]: #parameters-2

[11]: #postcommand-1

[12]: #parameters-3

[13]: #post-1

[14]: #parameters-4

[15]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise

[16]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean

[17]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error

[18]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String

[19]: #examples

[20]: #normal-usage

[21]: #sharding-usage

[22]: #handlers

[23]: #normal-handler

[24]: #sharding-handler

[25]: https://abal.moe/Eris/docs/Client

[26]: https://abal.moe/Eris/docs/Client

[27]: #issues

[28]: #post-event

[29]: #autopost-start-event

[30]: #events
