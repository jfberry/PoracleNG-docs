---
title: Common Error Messages
nav_order: 7
layout: default
parent: Installation
---

# Wrong version of node

```

> PoracleJS@4.0.2 start
> node .

internal/modules/cjs/loader.js:638
    throw err;
    ^

Error: Cannot find module 'worker_threads'
    at Function.Module._resolveFilename (internal/modules/cjs/loader.js:636:15)
    at Function.Module._load (internal/modules/cjs/loader.js:562:25)
    at Module.require (internal/modules/cjs/loader.js:692:17)
    at require (internal/modules/cjs/helpers.js:25:18)
    at Object.<anonymous> (/srv/PoracleJS/src/app.js:10:36)
    at Module._compile (internal/modules/cjs/loader.js:778:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:789:10)
    at Module.load (internal/modules/cjs/loader.js:653:32)
    at tryModuleLoad (internal/modules/cjs/loader.js:593:12)
    at Function.Module._load (internal/modules/cjs/loader.js:585:3)
```

You have the wrong version of Node.

# MariaDB configuration

```
Error: alter table `humans` add primary key `humans_pkey`(`id`) - Specified key was too long; max key length is 767 bytes
```

