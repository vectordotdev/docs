---
description: Integrate the Winston Node logger with Timber
---

# Winston

[Winston](https://github.com/winstonjs/winston) is a popular Node logger that you can use instead of the provided Timber logger. Timber integrates with Winston through the [`@timberio/winston` package.](https://www.npmjs.com/package/@timberio/winston)

## Installation

1. Import the Timber classes:  


   In ES6/Typescript, import both the `Timber` logger class and the Timber Winston transport class:  


   ```javascript
   import { winston } from "winston";
   import { Timber } from "@timberio/node";
   import { TimberTransport } from "@timberio/winston";
   ```

   For CommonJS, require the packages instead:  


   ```javascript
   const { winston } = require("winston");
   const { Timber } = require("@timberio/node");
   const { TimberTransport } = require("@timberio/winston");
   ```

2. Integrate with Winston, _**replacing `YOUR_API_KEY` and `YOUR_SOURCE_ID` accordingly**_:  


   ```javascript
   // Create a Timber client
   const timber = new Timber("YOUR_API_KEY", "YOUR_SOURCE_ID");

   // Create a Winston logger - passing in the Timber transport
   const logger = winston.createLogger({
     transports: [new TimberTransport(timber)],
   });
   ```

## Usage

Log as normal in Winston. See [Winston's usage docs](https://github.com/winstonjs/winston#usage) for more details.

```javascript
logger.log({
  level: "info", // <-- will use Timber's `info` log level,
  message: "Some message", // <-- will also be passed to Timber
});
```

