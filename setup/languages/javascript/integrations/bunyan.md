---
description: Integrate the Bunyan Node logger with Timber
---

# Bunyan

[Bunyan](https://github.com/trentm/node-bunyan) is a popular Node logger that you can use instead of the provided Timber logger. Timber integrates with Bunyan through the [`@timberio/bunyan` package.](https://www.npmjs.com/package/@timberio/bunyan)

## Installation

1. Install the NPM packages:  


   ```bash
   npm i @timberio/node @timberio/bunyan
   ```

2. Import the Timber classes:  


   In ES6/Typescript, import both the `Timber` logger class and the Timber Bunyan stream class:  


   ```javascript
   import { bunyan } from "bunyan";
   import { Timber } from "@timberio/node";
   import { TimberStream } from "@timberio/bunyan";
   ```

   For CommonJS, require the packages instead:  


   ```javascript
   const { bunyan } = require("bunyan");
   const { Timber } = require("@timberio/node");
   const { TimberStream } = require("@timberio/bunyan");
   ```

3. Integrate Timber with Bunyan, _**replacing `YOUR_API_KEY` and `YOUR_SOURCE_ID` accordingly**_:  


   ```javascript
   // Create a Timber client
   const timber = new Timber("YOUR_API_KEY", "YOUR_SOURCE_ID");

   // Create a Bunyan logger - passing in the Timber stream
   const logger = bunyan.createLogger({
     name: "Example logger",
     level: "debug",
     streams: [
       {
         stream: new TimberStream(timber),
       },
     ],
   });
   ```

## Usage

Log as normal in Bunyan. See [Bunyan's usage docs](https://github.com/trentm/node-bunyan#table-of-contents) for more details.

```javascript
logger.info("Hello from Bunyan");
```

