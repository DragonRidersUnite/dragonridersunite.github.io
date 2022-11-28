---
title: Efficient Input Checking
description: Quickly check if multiple keys are down, pressed, or released.
layout: post
---

Sometimes you may want to support multiple inputs for your game. Maybe you support WASD and the arrow keys. Maybe you support gamepads too. Giving players choice is important. Here's a way to check for multiple keys at once and the controller:

``` ruby
CONFIRM_KEYS = [:j, :z, :enter, :space]
def confirm?(inputs)
  inputs.controller_one.key_down&.a ||
    (CONFIRM_KEYS & inputs.keyboard.keys[:down]).any?
end

def tick(args)
  if confirm?(args.inputs)
    # do the stuff
  end
end
```

A few things on the implementation:

- `CONFIRM_KEYS` won't change in this example, so it's a constant.
- `inputs.controller_one.key_down&.a` attempts to check the A button on the first controller. If it's not connected, it'll return `nil` and be evaluately as falsy.
- `(CONFIRM_KEYS & inputs.keyboards.keys[:down]).any?` uses array intersection to check if any of the keys that are down are in the array we set, nifty! Faster than checking all of them separately.

The `args.inputs.keyboard.keys` hash is a great data structure to use as it contains values for all of the key states:

``` ruby
{ :down => [:enter], :held => [], :down_or_held => [:enter], :up => [] }
```
