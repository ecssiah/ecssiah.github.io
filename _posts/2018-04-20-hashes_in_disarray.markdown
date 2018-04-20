---
layout: post
title:      "Hashes In Disarray"
date:       2018-04-20 02:25:14 -0400
permalink:  hashes_in_disarray
---

In Ruby, there is a subtle difference between these two calls: `Array.new(3, {})` and `Array.new(3) { {} }`. They both do very similar things. They instantiate a new Array object that has 3 elements, and each of these elements has the value of an empty hash.

If you look at the contents of both objects after creating them, they will look identical.

```
[{}, {}, {}]
```

But you know what they say about looks.

The difference only appears once you add a new key into one of the hashes in the array. Now, you'll get two different results, proving that the arrays were actually different in some way.

```
arr = Array.new(3) { {} }
arr[0][:test_key] = "test string"
```

In this case, you probably get exactly what you were expecting.

```
[{:test_key=>"test string"}, {}, {}]
```

A new key/value pair has been added to the first hash in the array.

But the other instantiation does not produce this same result, and it led to a very confusing bug in my CLI gem project. This bug made it appear like there was some loop in my code that I couldn't find anywhere. A loop that none of my tests were able to locate.

Look at the other method and try to guess what it might do differently.

```
arr = Array.new(3, {})
arr[0][:test_key] = "test string"
```

The second line is identical. There is no trick there. It performs the exact same operation. The same key/value pair has been added to the first hash in the array, but this is the output:

```
[{:test_key=>"test string"}, {:test_key=>"test string"}, {:test_key=>"test string"}]
```

Now can you tell what is happening? It took me a good 20 minutes to figure out how the two other hashes were being filled with identical key/value pairs.

The punchline is that they aren't two *other* hashes. All three hashes in the second example are the exact same hash. They just have three different names. `arr[0]`, `arr[1]`, and `arr[2]` all refer to the exact same hash.

After finding myself perplexed by this problem for a while, I finally looked at the documentation for the Array class a little more carefully and found this...

> Note that the second argument populates the array with references to the same object. Therefore, it is only recommended in cases when you need to instantiate arrays with natively immutable objects such as Symbols, numbers, true or false.

Maybe that should be in bold.

Or maybe I should read a little more closely.

Either way, the lesson here is to remember that the same object can sometimes have more than one name in Ruby. If you need to create an array that is filled with **unique** hashes, then you have to pass the `Array.new` method a block that instantiates a new Hash either literally or with `Hash.new`. Even calling `Hash.new` as the second parameter to `Array.new` is not good enough. It will simply create *one* new Hash object and then refer to it for each array element.
