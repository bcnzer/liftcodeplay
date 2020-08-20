---
author: "Ben Chartrand"
title: "How to add Cypress to your Nuxt project"
date: "2020-08-19"
image: "ryan-parker-x_0zh4ntFMM-unsplash.jpg"
tags: [
  'Nuxt'
]
---

Let me show you how to add Cypress to your Nuxt project.

###### Photo by [Ryan Parker](https://unsplash.com/@dryanparker?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/cypress-tree?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# About Cypress

[Cypress](https://www.cypress.io/) is a fantastic e2e testing framework. I'm a huge fan of it because, like Vue.js and Nuxt
* it's easy to use and learn
* the [documentation](https://docs.cypress.io/guides/overview/why-cypress.html) is great
* it's so empowering! You don't have to be an automation testing expert

If you've ever done any automation testing, you'll be pleased to hear it's a single tool and has nothing do with Selenium.

Unfortunately Cypress is not offered natively by the Nuxt starter template but it's pretty easy to add.

# Installing Cypress

The first step is to add Cypress to your project. The document states you can add it as a straight NPM package but I believe it's better to add it as a dev dependency

`yarn add cypress --dev` 

or 

`npm install --D cypress`

# Configuration

## cypress.json file

This handy wee file will tell Cypress where to look for 

## package.json command
Ok so this is theoretically option but practically you really, really should add this to make your life easier.

## gitignore
ignore the screenshots and videos

# Optional but recommend configuration 

## package.json command

## Tell ES Lint to ignore

I quite like ES Lint but, for tests, it often got in the way so I told it to ignore the spec files. 

## Force Cypress to use a specific browser