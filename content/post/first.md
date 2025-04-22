+++
author = "P G" 
title = 'Multiple Language Support for ebitenengine in Go.'
date = 2025-04-13
description = "A basic tutorial on how to easily support multiple languages in ebitenengine."
tags = [
    "ebiten",
    "text",
    "translations",
    "maps",
    "fonts",
]
categories = [
    "ebiten",
    "games",
    "tutorial",
    "go",
    "golang",
]
series = ["Ebiten Guides"]
toc = true

draft = false
+++
# Summary

  * This tutorial will show you how to make a text abstraction that you can easily plug in buttons, menus etc and easily change your entire game language.No time to waste lets go !
<!--more-->
# COMING SOON


## Requirements
   - You will need Go installed and some pre-requisites for ebitenengine.
   * You can find the full instructions [here](https://ebitengine.org/en/documents/install.html)
    I suggest you make sure you have the example shown there running first. This will be the base for our tutorial.

# Intro
    First we have to load our .ttf font. We will the "embed" stdlib package to emded our font at compile time.
    Then we will create a text abstraction that we can use thruout our game. The final step will be to add the multiple languages map.
# 1 Font.
## Req.
* You will need to download a .ttf font file. Since this tutorial is about translations you will have to pick one that supports a wide range of languages. I will be using [Deja Vu Sans](https://www.fontsquirrel.com/fonts/dejavu-sans) for the tutorial.

### Embed and load.
1. This will be our intro to the "embed" package.




 
