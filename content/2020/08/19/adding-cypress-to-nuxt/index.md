---
author: "Ben Chartrand"
title: "How to add Cypress to your Nuxt.js project"
date: "2020-08-19"
image: "ryan-parker-x_0zh4ntFMM-unsplash.jpg"
tags: [
  'Nuxt'
]
---

Let me show you how to add Cypress to your [Nuxt.js](https://nuxtjs.org/) project. It's really easy but I do suggest a few extra bits of config.

###### Photo by [Ryan Parker](https://unsplash.com/@dryanparker?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/cypress-tree?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# Sample repo
Here is a link to a GitHub repo with an example: https://github.com/bcnzer/nuxt-cypress-starter-template

# About Cypress

[Cypress](https://www.cypress.io/) is a fantastic e2e testing framework. I'm a huge fan of it because, like Vue.js and Nuxt it is:
* easy to use and learn
* the [documentation](https://docs.cypress.io/guides/overview/why-cypress.html) is great
* it's so empowering! You don't have to be an automation testing expert

If you've ever done any automation testing, you'll be pleased to hear it's a single tool and has nothing do with Selenium.

# Installing Cypress

The first step is to add Cypress to your project. The document states you can add it as a straight NPM package but I believe it's better to add it as a dev dependency

`yarn add cypress --dev` 

or 

`npm install --D cypress`

# Configuration

## cypress.json file

Add this file to your project with the following content. This is not required if you use the default `cypress` folder but adding it allows you to change the paths to whatever you like. I personally have my tests under an `tests/e2e` folder and you may want to store your screenshots and videos elsewhere.

```
{
  "baseUrl": "http://localhost:3000",
  "fixturesFolder": "cypress/fixtures",
  "integrationFolder": "cypress/integration",
  "pluginsFile": "cypress/plugins/index.js",
  "screenshotsFolder": "cypress/screenshots",
  "supportFile": "cypress/support/index.js",
  "videosFolder": "cypress/videos"
}
```
I highly recommend you use the `baseUrl` value. Later on, when you tell your tests to browse to other pages, you can use relative URLs.

## Folders
Assuming you setup everything as per the above config file, you will need the following folders your project. You can use [the repo I mentioned above](https://github.com/bcnzer/nuxt-cypress-starter-template) but opening Cypress for the first time will create this content and add a bunch of helpful examples.
```
cypress
-> fixtures
-> integration
-> plugins
-> screenshots
-> support
-> videos
```

Let me explain each folder briefly
* [fixtures](https://docs.cypress.io/api/commands/fixture.html#Syntax) contain json files. You can leave it empty at first
* **integration** is where you'll write your Cypress tests. Each file should end with `.spec.js` i.e. `clubsetup.spec.js`
* [plugins](https://docs.cypress.io/guides/tooling/plugins-guide.html) allow you to extend or modify behaviour in Cypress. As per the config file above it expects an index.js file. I would suggest you add this by default
* [support](https://docs.cypress.io/guides/references/configuration.html) is the home of a configuration file. You can leave it empty at first, add to it over time
* `videos` and `screenshots` are, as the name suggests, are where they get stored. Feel free to set the path to these elsewhere

## .gitignore
Cypress can record screenshots and videos during the testing process. That's great but you likely don't want to store this in your repo, so add this to your `.gitignore` file

```
# Cypress videos and screenshots
/cypress/screenshots
/cypress/videos
```

## package.json command
Nuxt comes with a test command. You can edit it to run your Cypress tests. I chose to create a twos commands in my `scripts` section
```
  "scripts": {
    "e2e": "cypress open --browser chrome",
    "e2e:silent": "cypress run",
  }
```

The first one pops up the Cypress tool for you to choose which tests you run. Note that `--browser chrome` is optional. I'm using it to always test with Chrome by default.

The second will run the tests without UI.

## Tell ES Lint to ignore .spec.js files

I quite like ES Lint but, for tests, it often got in the way so I told it to ignore the spec files. 

If you don't already have one, add a file `.eslintignore` then add the following to it
```
cypress/integration/*.spec.js
```

## You're all setup!

Have fun!