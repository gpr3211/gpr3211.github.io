+++
author = "P G" 
title = 'Parsing Fonts and Displaying Text in Ebitengine'
date = 2025-04-13
description = "A basic tutorial on how to display text"
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

  * This tutorial will show you how to make a text abstraction that you can easily plug in buttons, menus etc .In Part 2 we will explore how to add multi-lang support and more dynamic text.No time to waste lets go !
<!--more-->


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

![Owl](https://raw.githubusercontent.com/gpr3211/gpr3211.github.io/refs/heads/master/content/post/1/tutorial.jpg)

## 0.2 Text
We are done with fonts, time to write our text abstraction. Lets start by opening ui/text.go.
```go
package ui

import (
	"github.com/hajimehoshi/ebiten/v2"
	"github.com/hajimehoshi/ebiten/v2/text/v2"
	"image/color"
)

type FontSize int

const NormalFontSize FontSize = 12
const LargeFontSize FontSize = 16

var ColorWhite = color.RGBA{255, 255, 255, 0}
var ColorBlack = color.RGBA{0, 0, 0, 1}
var ColorRed = color.RGBA{223, 10, 17, 1}
```
```go
	type Text struct {
	content string
	color   *color.RGBA
	size    FontSize
	x       int
	y       int
}

func NewText(str string, x, y int, size FontSize, c color.RGBA) *Text {
	return &Text{
		content: str,
		color:   &c,
		x:       x,
		y:       y,
		size:    size,
	}
}
```
Next we create some helper methods.
```go

func (g *Text) ChangeText(str string) {
	g.content = str
	g.Update()
}

func (t *Text) Move(x, y int) {
	t.x = x
	t.y = y
	t.Update()

}
func (g *Text) Resize(size FontSize) error {
	g.size = size
	g.Update()
	return nil
}

```
Finally we must define a Draw and Update methods to be used by the game engine.
```go

func (t *Text) Draw(screen *ebiten.Image) {
	op := &text.DrawOptions{}
	op.GeoM.Translate(float64(t.x), float64(t.y)) //set position
	op.ColorScale.ScaleWithColor(t.color)         //set colo
	text.Draw(screen, t.content, &text.GoTextFace{ // set font options
		Size:   float64(t.size),
		Source: currFont,
	}, op)
}
func (t *Text) Update() error { return nil }

```
And thats it for now. Time to finally jump to our main.go function and see the fruits of our labour.
## 0.3 Game
### Game Struct
Our base Game struct displayed nothing. Lets add an UI element to it and a NewGame function .
First we must import our ui package.
add this to your imports

```go
import (
	"log"
	"<yourmodname>/ui" // add ui package
	"github.com/hajimehoshi/ebiten/v2"
)
```


```go
type Game struct {
	UI *ui.Text
}
```
Your main function at this point should look like 
```go
func main() {
	ebiten.SetWindowSize(640, 480)
	ebiten.SetWindowTitle("Hello, World!")
	if err := ebiten.RunGame(&Game{}); err != nil {
		log.Fatal(err)
	}
}
```
We can create a new text object by simply 
```go
	hello := ui.NewText("Hello World!", 150,150,ui.LargeFontSize,ui.ColorWhite)
```
Now lets create a new game object and populate it with the text.
```go
	game := &Game{
	UI:	hello,
	}	
```
Now just plug new game struct into RunGame
```go
	if err := ebiten.RunGame(game); err != nil {
		log.Fatal(err)
	}
```
Lets run the game.
```bash
go run .
	Loading fonts
	Font loaded
```
### Draw and Update
You should see this, but the game screen is black ?!?!?! Well ofc we have to call Draw and Update.
Lets modify the methods.
```go

func (g *Game) Update() error {
	g.UI.Update()
	return nil
}

func (g *Game) Draw(screen *ebiten.Image) {
	g.UI.Draw(screen)
}
```
Now run the game. 
The Hello world text should appear.

![hello](https://raw.githubusercontent.com/gpr3211/gpr3211.github.io/refs/heads/master/content/post/1/hello_there.png)
Part 2 coming soon where we add multiple-lang support and more dynamic values. Hope you enjoyed :)



## full code for this post can be found [here](https://github.com/gpr3211/examples-blog/tree/master/p1) 
