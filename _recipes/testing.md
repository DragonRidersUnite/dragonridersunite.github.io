---
title: Testing
description: How to write automated tests to verify the functionality of your game code with DragonRuby Game Toolkit.
layout: post
author: Brett Chalupa
---

Writing code for your game is fun. How about writing code that verifies your code actually works? That's even more fun.

A word of causion: automated tests are a more advanced feature, but one that's quite useful.

DragonRuby Game Toolkit provides functionality right out of the box for writing tests. You'll notice in `mygame/app/` a file called `tests.rb`. That file contains the core foundations for writing tests for your game.

However, what's provides isn't exactly thorough. So this recipe will go over how to write tests for your game.

## Why Test

You might be thinking to yourself (maybe even saying this aloud): why would I even want to write tests for my code? I play my game, and I know it works! I don't want to take time to write tests, I just want to make my game. Ugh, tests are for suckers!

I hear you, I hear you. Testing (or lack thereof) has such dogma surrounding it. It's like brushing your teeth—a lot of people say you should be doing every night!

Whether or not you write tests is up to you, but they do provide value. A little bit of testing is better than none. Anytime you have code that has a conditional statement or does some sort of calculation or changes data, there's a chance it might do something unexpected. We write tests to be sure that the code does what we want it to in all possible permutations. It's also great for edgecases and weird data that gets passed around.

I'm a believe in writing tests. It's helped me catch bugs early, and it gives me more confidence. Testing also can help with designing your code. In general, code that's easier to test is easier to maintain. Win-win-win!

## Setup

Clear out `app/tests.rb`, as we'll want to have a bit of a simpler starting place. Replace everything in that file with this:

``` ruby
# Run the tests: ./dragonruby mygame --eval app/tests.rb --no-tick

def test_it_works(args, assert)
  assert.true!(5 + 5 == 10)
end

puts "running tests"
$gtk.reset 100
$gtk.tests.start
```

That's everything we need to be set up and ready to write and run tests.

## Running Tests

When you write code, there needs to be some way to run the tests. That comment at the top of our file is how you run it. You tell the DragonRuby executable to just evaluate our tests file and don't run the game loop. Here's that command again:

``` console
./dragonruby mygame --eval app/tests.rb --no-tick
```

Go ahead, give a try! It won't bite.

You'll see the output has something like this:

``` console
- 000000 [Game] running tests
- 000000 [Game] * TEST: gtk.test.start has been invoked.
- 000000 [Game] ** Running: test_it_works
- 000000 [Game] Passed.
- 000000 [Game] ** Summary
- 000000 [Game] *** Passed
- 000000 [Game] 1 test(s) passed.
- 000000 [Game] **** :test_it_works
- 000000 [Game] *** Inconclusive
- 000000 [Game] 0 test(s) inconclusive.
- 000000 [Game] *** Failed
- 000000 [Game] 0 test(s) failed.
- 000000 [Game] * INFO: New methods discovered.
** :test_it_works
```

That's the summary of our tests being run. It says that it ran one test and it passed. That's what we want! Awesome.

## A Basic Test

The code in `app/tests.rb` that we really care about is this:

``` ruby
def test_it_works(args, assert)
  assert.true!(5 + 5 == 10)
end
```

We define a method that's prepended with `test_` and tell it to take two parameters: `args` and `assert`. DragonRuby finds all of our methods that start with `test_` and calls them to run our tests. To write a test, all you do is define a method with that prefix. Sweet!

`args` is like the `args` that get passed into `#tick`. You know good ole args! You can access whatever you want in your test code. You could set something in `args.state`, call some code, and then verify it does what you want. More on that soon.

The second parameter, `assert`, is new! It's an object that we can call methods on. It's the core part of writing tests. You execute your code in your game and then you make assertions on what's returned or what's changed. There's `assert.true!` where you expect the value passed in to be truthy. There's also `assert.false!` and `assert.equal!`. We'll dig into using those before ya' know it.

Then `assert.true!(5 + 5 == 10)` is the actual test we've written. 5 + 5 does in fact equal 10, so it's true. And our assertion verifies that.

Go ahead and change what we pass to be `5 + 5 == 9`, save it, and run your tests.

Now we have this output:

``` console
- 000000 [Game] running tests
- 000000 [Game] * TEST: gtk.test.start has been invoked.
- 000000 [Game] ** Running: test_it_works
- 000000 [Game] Failed.
- 000000 [Game] ** Summary
- 000000 [Game] *** Passed
- 000000 [Game] 0 test(s) passed.
- 000000 [Game] *** Inconclusive
- 000000 [Game] 0 test(s) inconclusive.
- 000000 [Game] *** Failed
- 000000 [Game] 1 test(s) failed.
- 000000 [Game] **** Test name: :test_it_works
- 000000 [Game] false was not truthy.
```

Our test failed! Hey, it's working. That's good.

## A Real Test

But writing a test assertion with basic math isn't very useful in the context of our game. Let's test some real game code.

Our tests have access to _everything_ in `app/main.rb` and the code that's required there. Let's say, for example, that we had a method in our game that checks if a given rectangle-ish object is out of the bounds of another rectangle:

``` ruby
# checks if the passed in `rect` is outside of the `container`
# `container` can be any rectangle-ish data structure
def out_of_bounds?(container, rect)
  rect.left > container.right ||
    rect.right < container.left ||
    rect.bottom > container.top ||
    rect.top < container.bottom
end
```

There's a lot going on in those four lines. We've got four conditional parts, each of which compare different data from our two method parameters. That's a good candidate for a buggy implementation. A test can help us be sure this works.

One of the nice aspects of DragonRuby GTK code is that it's often functional and pretty simplistic, both of which make our code easier to test. We'll define our own test method, `test_out_of_bounds` (I tend to just use whatever method name I'm testing after the `test_` prefix, but you can do whatever you want).

``` ruby
def test_out_of_bounds(args, assert)
  grid = {
    x: 0,
    y: 0,
    w: 1280,
    h: 720,
  }
  assert.true!(out_of_bounds?(grid, { x: -30, y: 30, w: 24, h: 24 }))
  assert.true!(out_of_bounds?(grid, { x: 30, y: -50, w: 24, h: 24 }))
  assert.false!(out_of_bounds?(grid, { x: 30, y: 30, w: 24, h: 24 }))
end
```

Because of how `#out_of_bounds?` works, we don't even need to use `args` in our code. But as you can see, we just call our method, pass in data, and make assertions on it.

We have our first `assert.false!` too. It's just the inverse of `assert.true!`. Pretty nifty.

You could add even more assertions to this test for other locations. How would you test that a rectangle positioned to the far right and above the grid is properly out of bounds?

## Assertions

Let's break down the three core assertions:

### `assert.true!`

Expects the passed in value to be truthy (not `false` and not `nil`).

### `assert.false!`

Expects the passed in value to be falsey (`false` or `nil`).

### `assert.equal!`

We haven't used this one yet, but it's quite handy. It asserts that the first and second parameters are equal. For example: `assert.equal!(5 + 5, 10)`

### Failure Messages

All of the assertions accept an optional final parameter as a failure message to help you know what didn't quite go as expected.

``` ruby
assert.equal!(5 + 5, 10, "math didn't work!")
```

You don't always need a message. Totally up to you.

## Another Example

Let's say we have this method:

``` ruby
# strips away the junk added by GTK::OpenEntity
def open_entity_to_hash(open_entity)
  open_entity.as_hash.except(:entity_id, :entity_name, :entity_keys_by_ref, :__thrash_count__)
end
```

We could test it like this:

``` ruby
def test_open_entity_to_hash(args, assert)
  args.state.foo.bar = true
  args.state.foo.biz = false
  assert.equal!(open_entity_to_hash(args.state.foo), { bar: true, biz: false })
end
```

This slightly more advanced example makes use of `args.state` and `assert.equal!`. We now know that method is working as expected.

## What to Test

When it comes to testing your code, how far you want to take it is up to you. You could test everything, sure. Some people do! But I tend to think about it like this: what code has paths that aren't often exercised and obvious when broken? I try to test that code.

Here's an overview of what I try to test:

- Mathematical functions
- Code that processes things
- Code that transforms data
- Code that has edge cases that aren't often reached

One of the joys of working with just data and functions is that testing is quite simple, you pass in your data and test what your function does. If you use objects and construct more complex instances, you can test those too! But there's quite a bit more set up and state involved there. It's certainly a trade-off and decision for you to make when architecting your code.

Happy testing!

## Beyond!

If you're looking for a little bit more of an advanced approach to testing, check out [DragonTest](https://github.com/DragonRidersUnite/dragon_test). It's a community project that provides a custom DSL, more assertions, and a test runner that you can easily use on CI and locally.

It lets you write tests like this:

``` ruby
test :text_for_setting_val do |args, assert|
  it "returns proper text for boolean vals" do
    assert.equal!(text_for_setting_val(true), "ON")
    assert.equal!(text_for_setting_val(false), "OFF")
  end

  it "passes the value through when not a boolean" do
    assert.equal!(text_for_setting_val("other"), "other")
  end
end
```
