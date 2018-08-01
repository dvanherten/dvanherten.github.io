---
layout: post
title: Die Roller - Starting off on the wrong foot
categories: [self-improvement,dotnetcore]
fullview: false
comments: true
permalink: /2017/07/dieroller-part-2/
---

So as I alluded to in the first post, I wasn’t happy with the tiny bit of code that I had done so far. I literally had one class and one test just to get some probability calculations going. But there were two things that I didn’t like.

## Problem #1: It was wrong

You've probably heard it a million times. Don't write your test after your implementation or you'll just end up writing a passing test for a failing implementation. Yep. That's exactly what I did. The test simply used the same calculation I used in the implementation to vet the calculation was correct. But of course it wasn't.

That test wasn't supposed to serve much of a purpose initially, but I knew I was going to be changing this code to be a little more involved and wanted the backing test. However, I totally had the calculation backwards.

So word to the wise, write your test first, ensure it is actually validating what you want it to, and then implemented it. A face palm moment for sure.

For the curious, [here's the commit.]("https://github.com/dvanherten/DieRoller/commit/ac319e3f66f0b0447b15441d9f053d838c3df6a2#diff-1088ba0e8955643e802c3fad696e5857")

## Problem #2: Wrong responsibilities

The moment I had finished writing the first Die class I realized I’d goofed. There were two responsibilities in the 6-sided die class. One was the fact that the class was intended to be a representation of a THING. A die and a die does not have a probability on its own.

What I had introduced was a second consideration of a scenario. Passing in the desired roll to calculate probability shouldn’t have been a responsibility of a die. So as a first order of business I’ve gone and addressed that by shifting some of the code around. The main change I made was introduced the concept of a scenario and called it a Roll. When you Roll a Die, you have a desired result in mind. We'll see if that sticks.

<script src="https://gist.github.com/dvanherten/155fb8f01194474c5d26df9385498225.js"></script>

I knew I was going to have at least two types of dice for my situation (D3/D6) so I went ahead and created an abstraction for it. I hummed and hawed a little bit over interface vs abstract class, but as I didn't have any base behaviour I decided to move ahead as an interface. Easy enough to change later.

## Summary

So as you can tell, I've already made some bumps along the way. That's totally normal. The only difference here is that I'm telling the world about it. This all took place in a very short amount of time. Thinking about how I was going to write this up and then actually writing it was considerably longer, but it was fun to do and a good exercise for documenting my thought process.

More soon!