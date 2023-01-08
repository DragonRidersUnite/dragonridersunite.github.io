---
title: Renderstreams
description: Pro feature for recording gameplay as data, which can then be replayed and exported as video.
layout: post
author: Brett Chalupa
---

A neat Pro feature in  is that you can record the gameplay to a data file that can then be used to replay the game or be output to video.

## Record Gameplay

This command will output the gameplay to the `gameplay.render` file:

``` console
./dragonruby --set renderstream.filename gameplay.render
```

## Replay the Renderstream

You can then replay the just recorded file with:

``` console
./dragonruby-replay mygame gameplay.render
```

## Output to MP4

Using ffmpeg, `dragonruby-replay` can take the `*.render` file and convert it into an MP4 video. Useful for getting high-quality footage for sharing, editing trailers, and more.

``` console
./dragonruby-replay mygame gameplay.render gameplay.mp4
```

## References

- [Video explainer by Ryan C. Gordon](https://www.youtube.com/watch?v=6DT20eXnT88&t=39)
