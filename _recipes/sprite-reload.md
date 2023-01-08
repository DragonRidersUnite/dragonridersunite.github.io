---
title: Sprite Reload
description: Refresh all sprites in debug mode without having to restart the game.
layout: post
author: Brett Chalupa
---

DragonRuby Game Toolkit has live reloading of your Ruby code when your game runs in development, which is fantastic. It means quick cycles of making changes and trying them without waiting for your game to compile. DRGTK does not automatically reload your sprites when they change, though. But what if you could quickly reload them yourself?

This recipe goes over how to accomplish that with a simple keyboard button press when in debug mode (the mode DragonRuby runs in when you're making your game) but not in your live game.

``` ruby
SPATHS = {
  bg: "sprites/bg.png",
  player: "sprites/player.png",
}

def tick(args)
  args.outputs.sprites << [120, 120, 32, 32, SPATHS[:bg]]
  args.outputs.sprites << [300, 400, 32, 32, SPATHS[:player]]

  if !args.gtk.production && args.inputs.keyboard.key_up.i
    SPATHS.each { |_, v| args.gtk.reset_sprite(v) }
    args.gtk.notify!("Sprites reloaded")
  end
end
```

The key bit here is `args.gtk.reset_sprite("sprites/mysprite.png")`. It's a method that DRGTK has to refresh a sprite in the running game.

But there isn't a way to refresh all of the sprites, so the recipe above keeps track of all of the sprite paths in a constant hash `SPATHS` and accesses them through their keys. Added benefit: the path code is more concise and less prone to errors from typos.

Then, if the game is not running in production mode (i.e., you're developing it on your computer), if you press the <kbd>i</kbd> key, all of the sprites are refreshed.

You can of course change the key to be whatever you want.

Presto change-o! Your sprites are refreshed. This cycle of update the sprites, refresh, see how they feel, adjust some more, refresh, etc. makes for quite a nice dev experience.
