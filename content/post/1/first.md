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
* First we have to load our .ttf font. We will the "embed" stdlib package to emded our font at compile time.
    Then we will create a text abstraction that we can plug in windows/buttos w/e. The final step will be to add the multiple languages map.
## 0.1 Font
* You will need to download a .ttf font file. Since this tutorial is about translations you will have to pick one that supports a wide range of languages. I will be using [Deja Vu Sans](https://www.fontsquirrel.com/fonts/dejavu-sans) and [Thaleah Fat](https://www.dafont.com/thaleahfat.font)for the tutorial.

### Embed and load.
1. Lets begin by creating a ui module from the root of the project and creating an assets folder inide. 
```bash
mkdir ui
cd ui
mkdir assets
```
2. Move the .ttf files inside assets folder and create a text.go and font.go file in ui dir.

```bash
.
├── go.mod
├── go.sum
├── main.go
└── ui
    ├── assets
    │   ├── DejaVuSans.ttf
    │   └── ThaleahFat.ttf
    ├── font.go
    └── text.go
```
3. Open ui/font.go using your weapon of choice and add the following: 
```go
package ui

import (
	"embed"
	"fmt"
	"github.com/hajimehoshi/ebiten/v2/text/v2"
)

//go:embed assets/*
var fontAssets embed.FS

var DejaVuSans *text.GoTextFaceSource
var ThaleahFat  *text.GoTextFaceSource

var currFont *text.GoTextFaceSouce // we use this to set font inside our future text.Draw method

```
- note the fontAssets var MUST be declared on the following line after //go:embed
4. Save file and run **go mod tidy**

5. The next step is going to be loading our font into a *text.GoTextFaceSource .Lets take a look at the function we are going to use
```go

// NewGoTextFaceSource parses an OpenType or TrueType font and returns a GoTextFaceSource object.
func NewGoTextFaceSource(source io.Reader) (*GoTextFaceSource, error)
```
6. Say I just want to use .ttf fonts lets write down a function to parse the font we embedded.
```go
// LoadFont load a font to be used in text assets.
func LoadFont(name string, assets embed.FS) (*text.GoTextFaceSource, error) {
	font, err := assets.Open(fmt.Sprintf("assets/%s.ttf", name))
	if err != nil {
		return nil, err
	}
	defer font.Close()
	s, err := text.NewGoTextFaceSource(font)
	if err != nil {
		return nil, err
	}
	return s, nil
}
```
Next add the init function where we set currFont and the fonts.
```go
func init() {

	fmt.Println("Loading fonts")
	tf, err := LoadFont("ThaleahFat", fontAssets)
	if err != nil {
		log.Println("Failed to load font")
		panic(err)
	}
	dj, err := LoadFont("DejaVuSans", fontAssets)
	if err != nil {
		log.Println("Failed to load font")
		panic(err)
	}

	ThaleahFat = tf 
	DejaVuSans = dj

	currFont = ThaleahFat // set the font to be used later.
	fmt.Println("Font loaded")
}
```
As you can see there is nothing fancy going on.
This function will be called by an init() and errors will be handled there.

![Owl](https://raw.githubusercontent.com/gpr3211/gpr3211.github.io/refs/heads/debuglog/content/post/1/tutorial.jpg)

## 0.2 Text
    



 
