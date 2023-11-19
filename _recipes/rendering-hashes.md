---
title: Prefer Hashes over Arrays for rendering
description: Explore the advantages of using Hashes for rendering over Arrays.
layout: post
author: Owen Butler
---

## Summary

Prefer rendering using `Hash` representations of your primitives, over the `Array` based representations. The hash based form is quicker to render and has some readability benefits.

To understand why, we will go over `Array` based rendering, `Hash` based rendering then explain how the engine is able to use the `Hash` representation faster.

## Rendering basics/definitions

DragonRuby supports rendering a number of different primitives:

- Sprites
- Solids
- Borders
- Labels

You can tell DragonRuby to render these different primitives by "sending" them to the output arrays on args:

``` ruby
args.outputs.sprites << a_sprite # for sprites
args.outputs.solids  << a_solid  # for solids
args.outputs.borders << a_border # for borders
args.outputs.labels  << a_label  # for labels
```

DragonRuby is flexible in what it accepts to those arrays. Two of the types (and the subject of this recipe) are : `Array` and `Hash`.

## Array based rendering

`Array` based allows you to send `Array` objects at outputs. Here's an example Sprite, from the [docs](http://docs.dragonruby.org/#---how-to-render-a-sprite-using-an-array)

``` ruby
def tick args
  args.outputs.sprites << [
    640 - 50,                 # X
    360 - 50,                 # Y
    100,                      # W
    100,                      # H
    'sprites/square-blue.png' # PATH
 ]
end
```

You can see that the elements of the array at 0, 1, 2 etc correspond to the x, y, width and height values of the sprite. You can be flexible (to a point) on how much data you pass in to the array. [Here's the full set of sprites properties allowed to pass in the array](http://docs.dragonruby.org/#---more-sprite-properties-as-an-array).

## Hash based rendering

`Hash` based rendering is similar, but uses  keys to identify the properties of the sprite, instead of array locations.

Here's the same sprite example, expressed as a `Hash`:

``` ruby
def tick args
  args.outputs.sprites << {
    x: 640 - 50,
    y: 360 - 50,
    w: 100,
    h: 100,
    path: 'sprites/square-blue.png',
 }
end
```

Many more properties of sprites are allowed to be passed in the `Hash`. [Sprite Hash Properties](https://docs.dragonruby.org/#---rendering-a-sprite-using-a--hash-).

## Why prefer Hash over Array based rendering?

### Readability

There's a readaibility benefit to code that uses `Hash` to represent the primitives. Each property is named clearly. You can also re-order the properties in the hash if it increases readibility. You don't get that flexibility using the `Array` indexes.

### More advanced sprite properties available

- [Sprite Hash Properties](https://docs.dragonruby.org/#---rendering-a-sprite-using-a--hash-)
- [Sprite Array Properties](http://docs.dragonruby.org/#---more-sprite-properties-as-an-array)

If you'd like to use the more advanced sprite features, you'll need the `Hash` representation anyway.

### Performance

Last but definitely not least, the `Hash` based form of primitives is _much_ faster to process than the `Array` based one.

As the examples in the docs show, the `Array` based form of rendering is quite flexible:

- You can use the short form for `Label` with just x,y,text. You can also pass in more properties.
- You can use a short form for Sprite, with just x, y, w, h, path, or the longer form with additional properties.

This flexibility comes at a cost, as the engine needs to inspect each array passed in to determine accurately which properties are passed in which indexes. This inspection is costly.

With `Hash` based rendering, the engine still has to inspect the outputs to determine what's been passed in, but it is much faster due to:

- The path to check `Hash` primitives being highly optimised
- The stricter guarantees of what input will look like
- `Hash` access being generally very fast in DragonRuby

## More info

### warn_array_primitives!

DragonRuby ships with a helper method to log any `Array` based rendering usage:

- [warn_array_primitives!](http://docs.dragonruby.org/#-----warn_array_primitives!-)

Use it to neatly log all your usages, then convert them!

### Other primitive representations

We didn't touch on [Class based representations](https://docs.dragonruby.org/#---rendering-a-sprite-using-a--class-) of primitives, that can be the subject of a further recipe.