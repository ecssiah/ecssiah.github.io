---
layout: post
title:      "Transcending the Buddhabrot"
date:       2018-09-07 14:00:27 -0400
permalink:  transcending_the_buddhabrot
---


In the late 90s, a computer scientist named Melinda Green discovered a fascinating new way of looking at the Mandlebrot set.

Melinda's technique samples points in the complex plane, and then repeatedly applies the Mandelbrot equation to these points to see if they eventually land outside of an escape radius after a certain number of iterations.

An array of counters is used to track every point that is hit before a sample point escapes. The size of this array is chosen so that it will map directly to the pixels in the image that is being produced. Every time a point lands within the small area associated with a particular pixel it is incremented by one.

After a large number of points have been tested, this array of counters is used as a heat map to produce the image. Each count is divided by the largest value in the counters array, so that the largest value has a value of 1. This will be the brightest pixel in the image. All other pixels will have a brightness value somewhere between 0 and 1.

Here is Melinda's own page describing her process and displaying a number of examples: [Superliminal: Buddhabrot](http://superliminal.com/fractals/bbrot/bbrot.htm)

I wrote an application that expands her original technique.

Here is an animation I produced using my technique: [Green Fractals](https://www.youtube.com/watch?v=NHzehXtsRbo)

And here is the repo containing my implementation: [Green Fractals Repo](https://github.com/ecssiah/green-fractals)

Contributions are welcome!

My technique allows for a wide variety of generating functions, rather than just the traditional Mandelbrot generator. Each term also has a coefficient applied to it, and at each step in the process the previous value is conjugated before applying the equation again. This significantly changes the character of the images produced.

Here is an example of a generating function:

`w = conjugate(z)`

`z = pw^3 + qw^2 + rw + C`

`C` is the sample point for each test, which is chosen at random from points within the desired complex range.

This produces a generalized fractal based on the method developed by Green. I have called the range of fractals generated using this expanded method: Green Fractals.
