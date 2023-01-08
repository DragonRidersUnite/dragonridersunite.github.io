---
title: Fullscreen
description: Switch between windowed and fullscreen mode with ease while keeping track of the current state in DragonRuby GTK.
layout: post
author: Brett Chalupa
---

Games that target desktop operating systems often have a setting to display the game in fullscreen mode or windowed mode. This gives players the option to choose what they prefer. It's a good practice to have this option, so let's dig into how to do it with DragonRuby Game Toolkit.

DragonRuby GTK gives us this method change the game window to be fullscreen:

``` ruby
args.gtk.set_window_fullscreen(true)
```

If you want to return to windowed mode, pass in `false`:

``` ruby
args.gtk.set_window_fullscreen(false)
```

There's unfortunately no way to query the engine to know whether or not the game is fullscreen. So your best bet is to track this setting yourself in `args.state`. Here's how it would look to have the F key toggle between fullscreen and windowed mode:

``` ruby
def tick(args)
  args.state.fullscreen ||= false

  if args.inputs.keyboard.key_down.f
    args.state.fullscreen = !args.state.fullscreen
    args.gtk.set_window_fullscreen(args.state.fullscreen)
  end

  args.outputs.labels << { x: args.grid.w / 2, y: 520, text: "Press F to toggle fullscreen", alignment_enum: 1, size_enum: 8 }
  args.outputs.labels << { x: args.grid.w / 2, y: 320, text: "args.state.fullscreen: #{args.state.fullscreen}", alignment_enum: 1, size_enum: 4 }
end
```

Let's break down what that does:

- We initialize `args.state.fullscreen` to be `false` if it isn't set yet; we'll use this to track the fullscreen state ourselves
- If the F key is pressed, toggle the value. `!` negates a boolean value, so that'll toggle between true and false.
- We `#set_window_fullscreen` with the value in `args.state.fullscreen` after we change it

That's it! Not too shabby.

If you want to save that setting to a preference file so the setting is remembered when players launch the game again, you'd do something like:

1. set a default value
2. support toggling the setting
3. save to a file after the value changes
4. on game launch, check the save file
5. load the preference into `args.state.fullscreen` and call `#set_window_fullscreen`

This functionality is built into [Scale](https://github.com/DragonRidersUnite/scale), the DragonRuby GTK framework, so you can just use that or look at the source to learn how to do this.
