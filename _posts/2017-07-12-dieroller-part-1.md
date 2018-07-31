---
layout: post
title: Die Roller – A Github Nuget Experiment
categories: [self-improvement,dotnetcore]
fullview: false
comments: true
permalink: /2017/07/dieroller-part-1/
---

So a few friends of mine recently decided to take up collecting and playing the table-top miniature game Warhammer 40k. I collected a bunch of this stuff when I was a kid and I’m excited to finally have a reason to unpack it from storage and give it another go. Why is this relevant to a development blog? Simply because I now have a reason to make a small little library that would be useful for doing some number crunching related to the die rolls that take place in the game.

## Enter DieRoller

I’ve been looking for an excuse to make something open source and push it to Nuget. Having a simple generic dice rolling library would be a perfect trial run. It gets my feet wet in the building, packing, and pushing process using GitHub and NuGet so it’s a win on both those fronts. If I happen to take it farther and actually utilize it in an app then bonus, but this is more of a learning experience.

Throughout the process I’ll document my thinking around the implementations of the various required features. I’m certain I’ll end up changing my mind a bunch and refactoring as I go. Who knows how usable it will be when we are done, but it should be a fun journey.

## Requirements?

I threw together some basic requirements around what this little library would need to do. The Die Roller library should:

- For a given roll, provide the probability and a simulation of the roll.
  - Consider D6 and D3 as those are the main things used in 40k even though 40k references will not be present in this library specifically.
- Apply options to the roll such as re-rolling fails or just ones. You can only re-roll once.
  - This is an influenced rule from 40k. It is common to be able to re-roll any failed roll or just the ones.
- Apply a modifier to the roll such as +1 or -1.
- By default a roll modifier would come AFTER a re-roll, but ideally this would have an ability to toggle
- Simulation should provide a roll result of exactly what happened.
  - Rolled 5 dice targeting a 3+
  - Roll results 3, 5, 2, 1, 6. Re-rolling 2 & 1. Reroll was 2 & 4.
  - Modifier applied of +1.
  - Final result is 4, 6, 3, 5, 7 meaning all 5 pass the requirement of 3+.

## Follow along

If you actually care to I have pushed the first commit up to GitHub here so you can track the progress as we go along. The first commit has almost nothing in it and it already has something I’d like to change in it. I wasn’t kidding when I said I’d probably change my mind a bunch. I’ll write more about that shortly.

Away we go!
