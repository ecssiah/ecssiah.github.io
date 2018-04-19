---
layout: post
title:      "Searching For Euler"
date:       2018-04-19 00:56:20 -0400
permalink:  searching_for_euler
---


Sometimes the things you need aren't going to be there. Finding a way forward despite this is essential.

After the first full day of work on my project, I had made significant progress. The basic functionality of the entire program was in place, and it was interacting with the site I chose perfectly. Project Euler maintains a growing archive of interesting math problems (many of which make excellent coding challenges). I was scraping the site to allow a user to browse the archive through the command line and select a problem to get more details. Everything was displaying correctly, and I even managed to work out some of the difficult boundary case errors towards the end of the first day. So I went to sleep. It was nice things went so smoothly.

The next day, I returned to my project, hoping to write some tests and add a limited search feature as an extra challenge. I had changed nothing since the night before. I hadn't even used the computer. I ran the program, and it just sat there...doing absolutely nothing. There was no error message. The terminal simply did nothing. I interrupted the process and started trying to figure what tiny thing I must have changed to introduce this mysterious bug. Even after interrupting the process, there was no error message. I ran it again, and it just seemed to hang endlessly without doing anything. On the first day of work, I had run into some errors with the nokogiri library. It was giving an error that mentioned "incompatible library versions". I had fixed this problem by removing all of the relevant gems and restoring them with a command called `gem pristine`. It restores the gems to a "pristine" condition. This fixed the errors I had on day one, but it did absolutely nothing to solve this new issue that seemed to have arisen, out of nothing, from the aether.

*I had changed nothing. How could the program be broken now?*

That turned out to be the key to finding the solution to this problem. Even though I had no error messages to work with, I knew the application was hanging. It must still be doing *something*. I began looking into what was actually being executed. As soon as I found where the trail ended, I almost instantly knew what the problem was. I had changed nothing. So something else must have changed. I brought up my browser, and, sure enough, https://projecteuler.net/ was not responding. I went to a site that checks to see if a site is down for everyone, and it confirmed this was not just an issue with my internet. Project Euler was done *for everyone*. Somewhat suspiciously, it appeared to have went down only about an hour after I stopped working the night before.

At this time, I'm not sure whether the site going down might have been connected to all of the requests I was sending it. I did find a few other students in the forums talking about being blocked from accessing a site. Some maintainers consider scraping to be a violation of their terms, and they have tools that watch out for clues it is being done.

But my problem was solved. It was true. I had changed nothing. The resources I was relying on had changed. But while I waited for the site to return, I mocked up some test data to stand in for the missing site and added the simple search feature I wanted. I used the rest of my downtime to write up this blog post for the project. Keep moving. And remember that every project is a collobaration with an enormous, rapidly changing entity called the internet.
