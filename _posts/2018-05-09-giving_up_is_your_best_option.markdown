---
layout: post
title:      "Giving Up Is Your Best Option"
date:       2018-05-09 17:22:30 +0000
permalink:  giving_up_is_your_best_option
---


I've committed to doing something extra when it comes to each of the portfolio projects. I have some previous experience with coding, so I've been going through the curriculum pretty fast. In order to make sure I challenge myself, I've decided to take on an extra challenge when it comes to each project. Since the Sinatra project is the first project that took us from the text-based world of the command line into the graphical world of the browser, I decided I would add a significant visual component to this project.

I had been intrigued by vector graphics for a while. I knew that with HTML5 there was now a full-fledged canvas available for use in web development. By creating `<svg>` elements in your html files, you can build images rather than relying on predefined pixel-based images. SVG stands for "scalable vector graphics". The word "scalable" refers to one of the most significant differences of these images. They can be increased in size without losing resolution. The "vector" refers to the fact that these images are saved as a collection of points (represented as vectors). The images are rendered by connecting these points in ways defined by the markup used to define the element.

Here's a simple example:

```
<svg width="100" height="100">
   <circle cx="50" cy="50" r="40" />
</svg>
```

The svg element creates a canvas, and then inside those tags you can add definitions that draw on that canvas. In this example, a single circle is added to the canvas. Its center is placed at the vector point (50, 50), and it has a radius of 40. This setup allows a developer to create very complex graphical elements. They can even be animated. To get a feel for what is possible, take a look at the [Explained Visually](http://setosa.io/ev/sine-and-cosine/) site. If you open your browser's console, you'll be able to see that all of those complex animations they are doing are produced using svg elements.

So I set out to include some dynamic graphics in my Sinatra application. It started out well. I was going to create a web app that allowed the user to choose different parts and build up little cartoon lifeforms. By using svg elements, I could change the images as the user chose different body parts or altered the color of those parts using sliders. I spent the first day working almost exclusively on understanding more about the svg elements. I familiarized myself with an editor that allowed me to create the elements graphically, as opposed to messing around with the code to get these complicated shapes just right. I then began working on different ways to alter these elements based on user input. It was going pretty well, but I was quickly building up some rather dense code. And I was definitely wading well out into the deep end. As a result, some of the code started to look...well, rather ugly.

I was saving huge chunks of html as strings. I was forced to embed all kinds of Ruby logic into those strings. At one point, I noticed that I had javascript embedded in a large html string, and within that javascript, was embedded Ruby... That's not to say there may not be *some* legitimate application for this inception-style coding, but it started to feel like maybe I wasn't ready to swim in these deep waters. I could feel a strong current pulling at my feet.

Complicated code often means you're doing something in a hacky way, and that can be fine in small doses. Especially when you're just trying to get something working, but when you notice that your entire application is a thicket of complications, it may be time to reassess where you're going. 

There is a famous trap called the "Sunk Cost Fallacy". If someone has bet a large amount of money on a hand, it can often feel to them that it would be stupid to fold, because it would mean they would waste all of the money they put in. This leads them to continue betting money on what is likely a lost cause...therefore, leading them to lose even more money than they would have lost if they simply cut ties and folded.

After two days of work on this project (without really even getting started on the basic requirements), I decided that the most productive thing I could do was give up. Of course, this didn't mean throwing my hands in the air and abandoning the project. But it did mean putting the svg elements on the shelf for another day. In a few short hours, I had swapped out these complex svg graphics for simple PNG images that I could manipulating much easier using simple javascript callbacks and filters to change the colors. After that, the rest of the project went much more smoothly.

So, remember, sometimes giving up really is the best option you have. Don't forget what you learned. Return to deeper waters another day. And maybe next time you'll be wise enough to turn around sooner rather than making one last doomed bet.
