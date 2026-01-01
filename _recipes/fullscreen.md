---
title: Fullscreen
description: Switch between windowed and fullscreen mode with ease while keeping track of the current state in DragonRuby GTK.
layout: post
author: Brett Chalupa
---

Games that target desktop operating systems often have a setting to display the game in fullscreen mode or windowed mode. This gives players the option to choose what they prefer. It's a good practice to have this option, so let's dig into how to do it with DragonRuby Game Toolkit.

DragonRuby GTK gives us this method to check whether the game is running on a device where switching between fullscreen and windowed makes sense:

```ruby
args.gtk.can_resize_window?
```

You can expect that on most Destkop devices this would return true, while consoles and mobile devices return false.

From there, we have two methods to change the fullscreen state, one to toggle and one to set the state explicitly:

``` ruby
args.gtk.toggle_window_fullscreen
args.gtk.set_window_fullscreen(true/false)
```

You can also query the engine to see whether it is in fullscreen mode:

``` ruby
args.gtk.window_fullscreen?
```

Putting that all together, here is an example allowing the F key toggle between fullscreen and windowed mode, and using `#window_fullscreen?` to display the current state:

``` ruby
def tick(args)

  if args.inputs.keyboard.key_down.f
    args.gtk.toggle_window_fullscreen
  end

  args.outputs.labels << { x: args.grid.w / 2, y: 520, text: "Press F to toggle fullscreen", alignment_enum: 1, size_enum: 8 }
  args.outputs.labels << { x: args.grid.w / 2, y: 320, text: "fullscreen?: #{args.gtk.window_fullscreen?}", alignment_enum: 1, size_enum: 4 }
end
```

Let's break down what that does:

- If the F key is pressed, toggle fullscreen on the engine.
- Display a label showing the fullscreen state, using the query function.

That's it! Not too shabby.

If you wanted to have a setting that persisted, you'd do something like:

1. set a default value
2. support toggling the setting
3. save to a file after the value changes
4. on game launch, check the save file
5. call `#set_window_fullscreen` to set the desired state

This functionality is built into [Scale](https://github.com/DragonRidersUnite/scale), the DragonRuby GTK framework, so you can just use that or look at the source to learn how to do this.

For more info on DragonRuby GTK window functions see:

- [DragonRuby Runtime Window Functions](https://docs.dragonruby.org/#/api/runtime?id=window-functions)
