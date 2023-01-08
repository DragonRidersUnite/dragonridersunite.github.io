---
title: Debug Mode
description: Improve your development experience by rendering additional info and supporting hotkeys.
layout: post
author: Brett Chalupa
---

When you're making a game, you may want to code special functionality that only exists for you when you're building your game. This could opening up a leel editor, reloading the game on a key press, or rendering hitboxes. You don't want your players to see these things, so how would you only render it for you while you're making your game?

Enter debug mode!

When you launch the dragonruby executable, it runs in debug mode, meaning things like the console are available and `args.outputs.debug` is output. When you release your game, it runs in production mode.

So let's break down how to use debug mode to improve the development experience.

## Check the current environment

You can check if the game is running in production mode by calling `$gtk.production`. It returns a boolean value. It'll be `false` in development mode. Here's an example of how you could use it to make pressing the <kbd>r</kbd> key resets the game state:

``` ruby
def tick(args)
  if !$gtk.production && args.inputs.keyboard.key_down.r
    $gtk.reset
  end
end
```

Pretty nifty!

## Debug Helper

If you're relying upon that check a lot, introduce a `#debug?` method:

``` ruby
def tick(args)
  if debug? && args.inputs.keyboard.key_down.r
    $gtk.reset
  end
end

def debug?
  !$gtk.production
end
```

## `args.outputs.debug`

DragonRuby GTK has a really nifty feature for outputting labels, sprites, etc. only in debug mode. Here are some ideas of how to use that.

### Render Debug Properties

Want to track properties of a given data structure?

``` ruby
args.outputs.debug << [args.state.player.x, args.state.player.y, "health: #{args.state.player.health}"].label
```

That'll render the player's health right below their current position for you when developing but not for your players. Maybe you find it's a neat feature and you want to include it! That's cool too.

### Render Collision Details

Let's say you have some collision detection in your game and you want to see what sprite your entity is colliding with. In your collision check, render a rectangle around the collided entity:

``` ruby
collided_bottom = args.state.level.tiles.find { |t| t.y < @y && t.intersect_rect?(self) }
if collided_bottom
  rect = collided_bottom.rect
  args.outputs.debug << [rect.x, rect.y, rect.w, rect.h, 255, 20, 20].border
  @y = collided_bottom.top
end
```

## Outro

By making use of our custom `#debug?` method and `args.outputs.debug`, you can really make your life easier when building and testing your game. Boom!

## Additional Ideas

- Add toggleable hitbox rectangle rendering for sprites to visually see how everything's working
- Easily switch between scenes in your game with different number keys
- Ask testers what debug tools they could use to make it easier for them to efficiently test
