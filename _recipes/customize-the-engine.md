---
title: Customize the Engine
description: Change the README, default font, and more when working with DragonRuby GTK.
layout: post
author: Brett Chalupa
---

When you unzip the DragonRuby GTK engine, you'll notice there are a bunch of files provided by the engine. Because it's all self-contained and has no dependencies, everything that's used to make your game is there. Including some of the default files the engine uses. This recipe will go over how to change those default files to suit your game's needs. Understanding what these files do and changing them when you need to can give your game just a little bit of extra polish.

**A slight word of caution though!** If you change these files and then upgrade the engine version your game uses, your changes will be overwritten. Also, there's a slight risk of breaking your game by modifying these files incorrectly.

## Change the Default Font

This is an easy one! Just drop in a new `font.ttf` file to replace the default. This will change the default game font _and_ what's shown in the DRGTK console. You could make everything display in Comic Sans! Or Papyrus!

This is particularly useful if you don't want to always specify the font to use. But the downside to changing it is that if your new font isn't monospace, code shown in the console may be weirdly formatted.

## Changing the README

When your game is built and zipped up for the various operating systems, a README text file is included with the game. It includes this text:

```
This game powered by DragonRuby Game Toolkit: https://dragonruby.org/
```

While that's good to know (and a little bit of nice marketing for the engine), it's not useful at all for players. The README would ideally explain what the game is, how to play it, what the controls are, and go over any supported flags when launching the executable.

Edit the default README by changing the `.dragonruby/default_readme.txt` file. It's in a hidden folder (hidden folders start with `.`), so you will need to either open it with a terminal or adjust your file explorer to display hidden files.

Write a nice README for your game. It will help your players.

## Customize Web Build

When your game is built for the web target, it includes the needed JavaScript, HTML, and CSS to load your game and make it look nice. The defaults are for hosting on itch.io, but you may want to change some of the styles and behavior.

All of this code lives in `.dragonruby/stubs/html5`. Experiment with different looks and text and behavior. It's fun to hack around with this stuff a bit.
