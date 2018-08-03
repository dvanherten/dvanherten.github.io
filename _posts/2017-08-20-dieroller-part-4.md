---
layout: post
title: Die Roller - Abstracting dependencies on Random
categories: [self-improvement,dotnetcore]
fullview: false
comments: true
permalink: /2017/08/dieroller-part-4/
---

When building the Die Roller, I had two main use cases for it. Determine the probability of a given roll configuration and provide a simulation of the roll with all the information about what took place. When it comes to unit testing, probability calculations are more straight forward. They make sure that the code returns the expected result. Simulations are much more difficult to test because rolling a die is inherently random. Which means of course that you have to abstract the randomness away for the purposes of the tests.

My goals were:

1. Provide a mechanism to pass the exact numbers to use from tests to validate expected results.
1. Avoid having to know how many sides a die may have within the random abstraction
1. Attempt to make it as non-invasive as possible.

I ended up settling on the following interface.

```csharp
public interface INumberGenerator
{
    int GetNumber();
}
```

Note how I don't pass in any sort of condition to drive the number like the number of sides that the die may have. I didn't want to become needlessly complex in the abstraction where knowledge would need to be passed around. I merely want any single integer number back because that's what Random is going to do for me. I leave it up to the Die class to handle its concern that involves the number of sides the die has.

```csharp
public class Die
{
    // ...

    public int Sides { get; }
        
    public SingleRollResult Simulate(INumberGenerator numberGenerator)
    {
        var number = numberGenerator.GetNumber();
        number = Math.Abs(number);

        var remainder = number % Sides;
        return new SingleRollResult(this, remainder == 0 ? Sides : remainder);
    }

    // ...
}
```

If we were always generating numbers that were less than the total sides of the die we would have a problem, but we have to remember the intent here. At run time we will always be using a random generator interface which will provide a positive integer. This will return lots of really big numbers and the Die class will handle it fine. Remember that our intention is to make testing easier, not make this harder than necessary to work with.

```csharp
public class RandomNumberGenerator : INumberGenerator
{
    public static Random Random = new Random();
    public int GetNumber()
    {
        return Random.Next();
    }
}
```

So how do we get our own implementation in the tests? I've left this up to the RollBuilder. Yet another benefit of having this fluent creation pattern. Here is an example of the construction from one of the tests. If I leave out the WithNumbers call then it will fall back to the default of random numbers.

```csharp
var roll = RollBuilder.WithDie(Die.D6)
           .WithNumbers(new ForcedNumberGenerator(initialRoll, reroll))
           .Targeting(Target.ValueAndAbove(targeting))
           .WithReroll(Reroll.Ones)
           .Build();
```

As you can see I pass the numbers I want directly into it. There is a little bit of internal knowledge that the tester needs to be aware of. The first number will be used by the initial roll. The second number will be used for the reroll. The ForcedNumberGenerator implementation returns the numbers in the order they are passed in so this knowledge is required when building a test. I was okay with this trade-off because in any single die roll that is exactly what happens in life. You make your first roll and you may or not make your second. It feels natural so it should not be overly confusing.

Anyways, that's my little story about how I abstracted a system dependency on Random. It's a common enough practice to handle this in some fashion for testing and often also comes up for DateTime.Now as many applications have a dependency on dates.

I'm well on my way with implementing the modifier requirement of the Die Roller (tests first!) which has me wondering what best practices are when it comes to validating and testing complex calculations. I'll be writing something up about that experience soon. As always you can check out the project as it is coming along over on [Github](https://github.com/dvanherten/DieRoller).
