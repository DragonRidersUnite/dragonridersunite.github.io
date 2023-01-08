---
title: Metadata
description: Define and access metadata config values in your game.
layout: post
author: Brett Chalupa
---

## Metadata Explained

When you define values in `metadata/game_metadata.txt`, it may not be immediately apparent of what the base values are for.

- `devid` is your itch.io username, useful when combined with `dragonruby-publish`
- `gameid` is the slug of your game on itch.io, a.k.a. the URL path it's accessible at, also useful with `dragonruby-publish`

The rest of the values are pretty self-explanatory.

## Accessing Metadata

When you define values in files in the `metadata/` directory of your source, you can access them within your game using `args.cvars`. That returns a hash of all of the values that are set, which you can then access. So to get the version set of your game, you'd do this:

``` ruby
args.cvars['game_metadata.version'].value
```

An example usage of this would be for when creating save data, you could specify which version of the game was used so that if you change the data structure and need to migrate it across releases, you'd have that data accessible.
