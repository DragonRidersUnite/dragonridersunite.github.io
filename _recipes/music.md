---
title: Working with Music
description: How to play, pause, loop, mute, and control music.
layout: post
---

Music in DragonRuby GTK games should usually be in the OGG format because of the reduced file size compared to WAV files. Because music tracks are longer than sound effects, you want to be efficient with the size of your game that you distribute.

Take your OGG file and put it in the `mygame/sounds/` directory. Let's say you have a music file called `forest.ogg` that's the forest music theme for you game.

To start playing that track at the beginning of your game, you need to specify the music details in the `args.music` hash with a given key:

``` ruby
def tick(args)
  # only start it once, not on every tick
  if args.state.tick_count == 1
    args.audio[:music] = { input: "sounds/forest.ogg", looping: true }
  end
end
```

The key can be anything, but in this case we set it to `:music`. This matters because you want to have a different key for multiple sounds playing at the same time. `looping: true` makes it so the track repeats when it finishes.

Within your game when developing, you can open the console and check `$gtk.args.audio[:music]` to see the current details about the music track like play position.

[View the official docs on `args.audio`.](http://docs.dragonruby.org.s3-website-us-east-1.amazonaws.com/#--docs---gtk--args#audio-)

## Checking When a Track is Done

Let's say you have a music track that isn't looping and you want to know when it's done to play another track.

If you check `args.audio[:music]` (or whatever your key is for the track you're playing) and the value returns `nil`, then you know that music track finished played. Here's a snippet of what that'd loop like:

``` ruby
def tick(args)
  # start with track 1
  if args.state.tick_count == 1
    args.audio[:track1] = { input: "sounds/t1.ogg", looping: false }
  end

  # play track 2 if track 1 is finished and track 2 isn't playing
  if args.audio[:track1].nil? && args.audio[:track2].nil?
    args.audio[:track2] = { input: "sounds/t2.ogg", looping: false }
  end
end
```

That code is a bit simplistic, but it'll get you started on building a more complex music manager in your game.

## Pausing a Track

A track can be paused by setting the `paused` value to be true:

``` ruby
def tick(args)
  # start with track 1
  if args.state.tick_count == 1
    args.audio[:track1] = { input: "sounds/t1.ogg", looping: false }
  end

  # pause and unpause on p key press
  if args.inputs.keyboard.key_down.p
    if args.audio[:track1].paused
      args.audio[:track1].paused = false
    else
      args.audio[:track1].paused = true
    end
  end
end
```

## Stopping a Track

You can stop a track by setting it's `args.audio` key value to `nil`. Example:

``` ruby
def tick(args)
  # start with track 1
  if args.state.tick_count == 1
    args.audio[:track1] = { input: "sounds/t1.ogg", looping: false }
  end

  # stop the music after 100 ticks
  if args.audio[:track1] && args.state.tick_count > 100
    args.audio[:track1] = nil
  end
end
```

## Changing Volume

The hash you set for your audio track has a `gain` property that can be used to set the volume:

``` ruby
def tick(args)
  # play at a slightly reduced volume
  if args.state.tick_count == 1
    args.audio[:track1] = { input: "sounds/t1.ogg", looping: true, gain: 0.8 }
  end
end
```

It's also accessible to change on the fly, so let's say there's a method to change the volume globally of the game:

``` ruby
def change_volume(args, new_gain)
  args.state.gain = new_gain

  args.audio.each do |_, a|
    a.gain = args.state.gain
  end
end
```

That changes the gain of every currently running audio file (e.g. your music that's playing). If you set it in the `args.state.gain` and use that value with a default for your music initialization and sound effects, that code will be all set for the global setting.
