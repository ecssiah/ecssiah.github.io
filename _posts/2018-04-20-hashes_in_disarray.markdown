---
layout: post
title:      "Hashes In Disarray"
date:       2018-04-20 06:25:13 +0000
permalink:  hashes_in_disarray
---

In Ruby, there is a subtle distinction between these two calls: `Array.new(3, {})` and `Array.new(3) { {} }`. They both do very similar things. They instantiate a new Array object that has 3 elements, and each of these elements has the value of an empty hash.

But if you add a new key into one of the hashes in these Arrays, you'll see that you get two different results.

```
arr = Array.new(3) { {} }
arr[0][:test_key] = "test string"
```

In this case, you probably get exactly what you were expecting.

```
arr => [{:test_key=>"test string"}, {}, {}]
```

A new key/value pair has been added to the first hash in the array.

But the other instantiation does not produce this same result, and it led to a very confusing bug in my CLI gem project. This bug made it appear like there was some loop in my code that I couldn't find anywhere.

Look at the other method and try to guess what it might do differently.

```
arr = Array.new(3, {})
arr[0][:test_key] = "test string"
```

The second line is identical. There is no trick there. The exact same key/value pair has been added to the first hash in the array, but this is the output:

```
arr => [{:test_key=>"test string"}, {:test_key=>"test string"}, {:test_key=>"test string"}]
```

Now, can you tell what is happening? It took me a good 20 minutes to figure out how the other two hashes were being filled with identical key/value pairs.

The punchline is that they are not two "other" hashes. All three hashes in the second example are the exact same hash. They just have three different names. `arr[0]`, `arr[1]`, and `arr[2]` all refer to the exact same hash.

After finding myself perplexed by this problem for a while, I finally looked at the documentation for the Array class a little more carefully and found this...

> Note that the second argument populates the array with references to the same object. Therefore, it is only recommended in cases when you need to instantiate arrays with natively immutable objects such as Symbols, numbers, true or false.

Maybe that should be in bold.

Or maybe I should read a little more closely.

Either way, the lesson here is to remember that the same object can sometimes have more than one name in Ruby. If you need to create an Array that is filled with **unique** hashes, then you have to pass the new method a block that instantiates a new hash either literally or with Hash.new. Even calling Hash.new as the second parameter to Array.new is not good enough. It will simply create *one* new Hash object and then refer to it for each array element.
