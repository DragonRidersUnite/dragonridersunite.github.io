---
title: Enumberable
description: Intermediate-to-advanced usages of methods when working with collections.
layout: post
---

## Push Item to Front of Array

Use `#unshift` to push things to the front of an array. Kinda useful if you want, let's say, display a list of items in reverse chronological order:

``` ruby
def tick args
  args.state.input_log ||= []

  if args.inputs.controller_one.key_down.a
    args.state.input_log.unshift("A pressed @ #{Time.now.to_s}")
  end

  args.outputs.labels << args.state.input_log.map.with_index { |l, i| [100, 500 - i * 32, l] }
end
```
