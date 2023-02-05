---
title: Spritesheet
description: Rendering different sprites from the same image file with DragonRuby Game Toolkit.
layout: post
author: Brett Chalupa
---

When making games, it's common to have a spritesheet—a single image that contains multiple sprites within it that you slice out and display. Maybe it's the running animation of your player or a collection of different terrain types. It be be helpful to use spritesheets to group similar images together or reduce the number of files in your game.

Let's assume we have an image `sprites/spritesheet.png`, which is a 16x16 pixel image with four tiles in it. The key part of rendering just one tile as a sprite with DragonRuby GTK is to specify `tile_x`, `tile_y`, `tile_w`, and `tile_h` in your sprite Hash:

``` ruby
def tick(args)
  args.outputs.sprites << {
    path: 'sprites/spritesheet.png',
    # this is the good stuff, we render a portion of our image
    tile_w: 180,
    tile_h: 180,
    tile_x: 0,
    tile_y: 180,
    w: 128, # draw our sprite large!
    h: 128,
    x: args.grid.w / 2 - 64,
    y: 380,
  }
end
```

Here's an enlarged version of the spritesheet:

![square of four pixel smiley faces](/assets/spritesheet.png)

What gets drawn:

![purple slat face](/assets/spritesheet-tile.png)

`tile_x` being `0` means start at the left of the sprite, and then `tile_y` being `8` says to draw 8 pixels down from the top of the sprite.

Change `tile_x` and `tile_y` to render different tiles within the spritesheet.

When you specify the `w` and `h` of the sprite, you can enlarge the slice of the spritesheet we are rendering. Try changing those values too and see what happens.

## Tile Map

When working with more complex spritesheets, you may want to define a tilemap which specifies the coordinates and size of a given tile in the spritesheet. We'll use a Ruby Hash for this. And we'll animate it by changing the tile every 16 ticks.

``` ruby
def tick(args)
  args.state.tile_index ||= 0

  # here's our tile map!
  tiles = {
    0 => { x: 0, y: 0, w: 180, h: 180 },
    1 => { x: 180, y: 0, w: 180, h: 180 },
    2 => { x: 0, y: 180, w: 180, h: 180 },
    3 => { x: 180, y: 180, w: 180, h: 180 },
  }

  # change the tile every 16 ticks
  if args.state.tick_count % 16 == 0
    args.state.tile_index += 1
    if args.state.tile_index >= 4
      args.state.tile_index = 0
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

Here's an example image that coordinates to the index values of the tile map for our spritesheet:

![square of four pixel smiley faces with index vals](/assets/spritesheet-labeled.png)

This is one way to animate with DragonRuby GTK, You change the tile from the tile map and then render it. Using a tile map is more flexible because you could have tiles of different sizes within your spritesheet, which can be handy.

It wouldn't be too much of a stretch to change the keys of our hash to instead be symbols that we could easily reference. Imagine we had a terrain spritesheet:

``` ruby
  tiles = {
    :grass => { x: 0, y: 0, w: 180, h: 180 },
    :mountain => { x: 180, y: 0, w: 180, h: 180 },
    :water => { x: 0, y: 180, w: 180, h: 180 },
    :forest => { x: 180, y: 180, w: 180, h: 180 },
  }

tile = tiles.fetch(:grass)
```
