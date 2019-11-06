---
layout: post
title:  "How I automated my Tinder... quick and dirty edition"
date:   2019-09-16 10:25:04 -0500
categories: ml
---
I think I am a gorgeous looking man. So, I look for love on dating apps, cause real world dating is beneath me.

Tinder is a highly effective tool if you have money for a plus subscription (for them unlimited swipes and shizz) and patience to swipe till eternity if you are as gorgeous as me. But there is a tiny problem in my case... `I am LAZY!`

The fear of being lonely, and laziness to do anything about it is a dangerous cocktail. But luckily I have some skills to code and money to get a Tinder Plus subscription. Here we are gonna talk about just one of those aspects. And you might have guessed it, I'm not gonna talk about how you can make extra bucks to buy that subscription (hint: [get a job](https://www.linkedin.com)).

#### What was my igniter idea (no pun intended)?

Make the swipe activity automatic, based on the things I liked in a profile (hint: I am shallow, LOL, judge me now).
So, I wanted to build some code that can automate my swiping activity on my phone. HOWEVER, I don't have that level of skills with iOS programming.

Time went by and I eventually discovered Tinder's web application. That sparked an idea... how about I just make API calls to Tinder and make my swiping activity fully automated (risking my account getting banned and all that). I scoured the internet and tried some solutions on github but none of them worked for me properly, cause I use a phone number authentication. Some of those are linked here, you can give them a try if you are feeling up for a challenge.

[pynder](https://github.com/charliewolf/pynder)

[python-TinderAPI](https://github.com/MMcintire96/python_TinderAPI)

[Tinder](https://github.com/fbessez/Tinder)

[tinder-api-python](https://github.com/Vikash-Kothary/tinder-api-python)

#### So what approach did you take Shifu Swarup?

My approach is pretty pedestrian and not elegant AT ALL! The approach was to see what was on my laptop screen and make a decision based on that, just like a human would. That has its own can of worms, which I will open up later. But let's first see how to make a pretty basic automation if you happen to have a spare laptop... either a windows or a mac or a full size linux distro because I wasn't able to run it properly on my Raspberry Pi (The profile cards didn't show up at all).

An initial training was required for the algorithm to work. I needed something to grab So, The following code was useful to get started.
