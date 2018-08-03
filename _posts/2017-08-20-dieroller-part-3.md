---
layout: post
title: Die Roller - Outside In
categories: [self-improvement,dotnetcore]
fullview: false
comments: true
permalink: /2017/08/dieroller-part-3/
---

So I went and did it again. Not too long after writing the last post did I realize I started from an angle I shouldn't have. Again.

## Fluent Syntax

I knew when I started this that I wanted to have a fluent syntax for setting up the roll scenarios. This will allow the user of the library (me) to determine the conditions of constructions more easily without worrying about the underlying dependencies and construction rules. I'll also be free to leave things internal to make things easier to change later as long as I allow the RollBuilder to continue functioning the same way.

Essentially something like this:

```csharp
var roll = new RollBuilder(DetermineDie())
               .Targeting(GetTarget())
               .WithRerolls(GetRerolls())
               .WithModifier(GetModifier())
               .Build();
```

Essentially it allows the consumer to build mechanisms around the components of a roll. The GetTarget() or GetRerolls() will likely be based on some kind of ruleset and I believe the fluent syntax caters well to how that would work.

So great. I knew I wanted to do a fluent syntax so why didn't I start here? Good question. I had intended to, but for one reason or another I started on the implementation of a Roll first when I should have been thinking about how it was going to be used. Which brought me to the tests and a much more TDD approach.

## The Tests

So I started by re-writing the tests. Here's what the body of a simple test looked like with no reroll and no modifier.

Before:
```csharp
var die = new D6Die();
var expectedProb = (7 - requiredRollOrHigher) / 6m;
var roll = new Roll(die, requiredRollOrHigher);
_output.WriteLine($"Probability: {roll.Probability}");
roll.Probability.Should().Be(expectedProb);
```
After:
```csharp
var roll = RollBuilder.WithDie(Die.D6)
    .Targeting(Target.ValueAndAbove(valueAndAboveTarget))
    .Build();

// Calc - SuccessfulSideCount / Sides
var expectedProbability = (7 - valueAndAboveTarget) / 6m;
CheckProbability(roll, expectedProbability);
```

The initial test would break if I changed aspects of the constructor or other Roll concerns. There was no abstraction from the construction process and it required understanding the inner workings of the classes more than I liked. The test as it is now will only change if I actually change something about the public interface of the builder or the method to calculate the probability. As I plan to treat this as an API on Nuget with Semantic Versioning, I want to be aware of when I break the public interface. I do not expect as much churn on the builder side of things so the fluent syntax isn't just nice to work with, it also serves as a separation from how the classes work. So it makes perfect sense for why the unit tests would want to be written the same way as the consumer would be using it.

## Validation of Feelings

Gregory Brown tweeted this out a few days ago and it really hit home on why this worked out for me. OUTSIDE-IN!

<div style="text-align:center">
<blockquote class="twitter-tweet" data-lang="en">
Outside-in thinking and an emphasis on avoiding incidental damage to systems is the main takeaway from <a href="https://twitter.com/hashtag/TDD?src=hash">#TDD</a>. Automated testing isn't.
— Gregory Brown (@practicingdev) <a href="https://twitter.com/practicingdev/status/897899045003382784">August 16, 2017</a>
</blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
</div>

This change was because I was thinking about my API from the outside. How I wanted to use my API was driving the decisions around the public API as it should. By knowing how I wanted to use the library it made filling in the implementation much easier. It also feels more domain driven since it matches what you would be thinking in 40k.

> I'm making a hit roll. I have to hit on a 3+, I get to reroll my ones, and then I'll apply the -1 to hit because my enemy is in stealth

Props to Gregory Brown for putting what I was feeling so succinctly into words.

## Moving on

I'll likely post next about an abstraction that was necessary when it came to testing the roll simulations. That'll give me some time to figure out how roll modifiers will work. Here goes!