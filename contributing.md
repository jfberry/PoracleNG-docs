---
title: Contributing
nav_order: 12
layout: default
---

# Contributing to PoracleJS

## I need help
Best place to get your questions answered is our [Discord](https://discord.gg/AathPCp).   
Should that not succeed in solving your problem, you're also welcome to [raise an issue on GitHub](https://github.com/KartulUdus/PoracleJS/issues).  

## How do I get started contributing?

The project has its own developer dependencies that are automatically installed along with `npm i`.    
It includes packages to run the [wiki](https://muckelba.github.io/poracleWiki/) and check your code against the project [style guide](https://github.com/KartulUdus/PoracleJS/blob/master/.eslintrc.json)  

Linting rules will be checked automatically by travis; however, should you wish to save some time, you can run the check locally by running:  
`npm run lint` (please note,  Windows users may have to run `node_modules\.bin\eslint app\src` manually)

The `develop` branch is used for all changes, which are folded into `master` when we reach an island of stability (usually every
couple of months).  In truth though, most active users will drink from the firehose and live on develop as we don't let bugs 
last for very long!

### Submitting a helpful pull request

* **Description** - Describe in detail the changes you made. If you add or remove specific libraries, frameworks, etc, please list the specific frameworks. Any movements between files.
* **Motivation and Context** - What’s the need for this change? What issue does it solve? If there’s an open issue, please write this exact phrase: “Fixes #prnumber” This will automatically close the issue when the pull request is merged.
* **How has this been tested?** - Please include the details of your test.
* **Types of changes & Checklist** - Please put a x in boxes that apply. Make sure it looks like [x], instead of [x ] or [ x], so you get a nice checkbox.

### Being a contributor

If you have made a PR that introduces a useful feature to the project, you may be given a special "Contributor" role in discord.  
the dark blue color is guaranteed* to give you all the attention you need and people might actually see you as a slightly less unimportant person!

Gone are the days of you being neglected! Take matters in your own hands!