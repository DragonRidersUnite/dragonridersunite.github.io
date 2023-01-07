---
title: Spritesheet
description: Rendering different sprites from the same image file with DragonRuby Game Toolkit.
layout: post
---

When making games, it's common to have a spritesheet–a single image that contains multiple sprites within it that you slice out and display. Maybe it's the running animation of your player or a collection of different terrain types. It be be helpful to use spritesheets to group similar images together or reduce the number of files in your game.

Here's how to work with them in DragonRuby Game Toolkit.

The most important part of all of this is that when you render a sprite with DragonRuby GTK, you can specify `tile_x`, `tile_y`, `tile_w`, and `tile_h` in your sprite Hash:

``` ruby
def tick(args)
  args.outputs.sprites << {
    path: 'sprites/spritesheet.png',
    # this is the good stuff, we render a portion of our image
    tile_w: 8,
    tile_h: 8,
    tile_x: 0,
    tile_y: 8,
    w: 128, # draw our sprite large!
    h: 128,
    x: args.grid.w / 2 - 64,
    y: 380,
  }
end
```

This takes our image, spritesheet.png, which is a 16x16 pixel image with four tiles in it and renders the bottom left tile.

Here's an enlarged version of the spritesheet:

![square of four pixel smiley faces](/assets/spritesheet.png)

What gets drawn:

![purple slat face](/assets/spritesheet-tile.png)

`tile_x` being `0` means start at the left of the sprite, and then `tile_y` being `8` says to draw 8 pixels down from the top of the sprite.

Change `tile_x` and `tile_y` to render different tiles within the spritesheet.

## Tile Map

When working with more complex spritesheets, you may want to define a tilemap which specifies the coordinates and size of a given tile in the spritesheet. We'll use a Ruby Hash for this. And we'll animate it by changing the tile every 16 ticks.

``` ruby
def tick(args)
  args.state.tile_index ||= 0

  # here's our tile map!
  tiles = {
    0 => { x: 0, y: 0, w: 8, h: 8 },
    1 => { x: 8, y: 0, w: 8, h: 8 },
    2 => { x: 0, y: 8, w: 8, h: 8 },
    3 => { x: 8, y: 8, w: 8, h: 8 },
  }

  # change the tile every 16 ticks
  if args.state.tick_count % 16 == 0
    arg.state.tile_index += 1
    if args.state.tile_index >= 4
      arg.state.tile_index += 0
    end
  end

  tile = tiles[args.state.tile_index]

  args.outputs.sprites << {
    path: 'sprites/spritesheet.png',
    tile_w: tile.w,
    tile_h: tile.h,
    tile_x: tile.x,
    tile_y: tile.y,
    x: args.grid.w / 2 - 64,
    y: 380,
    w: 128,
    h: 128,
  }
end
```

This image coordinates to those index values of the spritesheet:

![square of four pixel smiley faces with index vals](/assets/spritesheet-labeled.png)

There are other ways to animate with DragonRuby GTK, but this just shows how you'd use a changing value to get the tile you want from the tile map and then render it. This is more flexible because you could have tiles of different sizes within your spritesheet.

It wouldn't be too much of a stretch to change the keys of our hash to instead be symbols that we could easily reference. Imagine we had a terrain spritesheet:

``` ruby
tiles = {
  :grass => { x: 0, y: 0, w: 8, h: 8 },
  :mountain => { x: 8, y: 0, w: 8, h: 8 },
  :water => { x: 0, y: 8, w: 8, h: 8 },
  :forest => { x: 8, y: 8, w: 8, h: 8 },
}

tile = tiles.fetch(:grass)
```
