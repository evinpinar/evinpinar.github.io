---
layout: post
title: Statistical Paradoxes
categories: [misc, tech]
comments: true
---

While reading Judea Pearl's [The Book of Why](http://bayes.cs.ucla.edu/WHY/), I digged a bit more into the statistical paradoxes and decided to sum the most interesting ones here. They work as brain-teasers and eye-openers, and it would be helpful to integrate them into our daily critical thinking and decision-making processes.


## Monty Hall Problem

Let's assume that you're on a game, and there are three doors upon you where one leads to a car and the others lead to goats. You pick the door #1, and the host opens one of the goat-doors. Now, you can either stay on your door, or switch your door. What would you do?

![](/images/monty.png "Monty Hall")

You should switch your door, if the host did not choose the door at random. Here is why:

| Door 1 | Door 2 | Door3  | Switch  | Stay
| ------ | ------ | ------ | ------- | ------
| Car    | Goat   | Goat   |  Lose   |  Win
| Goat   | Car    | Goat   |  Win    |  Lose
| Goat   | Goat   | Car    |  Win    |  Lose

So, if you switch the door, the possibility of win is 2/3 whereas it is 1/3 if you choose to stay. 
This is not really intuitive before seeing this table.

However, if the host would choose a random door (no matter it is goat or car), then staying would lead us to a higher chance of winning. Because this time (you picked door 1):

| Door 1 | Door 2 | Door3  | Door opened | Switch  | Stay
| ------ | ------ | ------ | -------     |-------  | ------
| Car    | Goat   | Goat   |       2     |  Lose   |  Win
| Car    | Goat   | Goat   |       3     |  Lose   |  Win
| Goat   | Car    | Goat   |       2     |  Lose   |  Lose
| Goat   | Car    | Goat   |       3     |    Win  |  Lose
| Goat   | Goat   | Car    |      2      | Win     |  Lose
| Goat   | Goat   | Car    |      3      |   Lose  |  Lose


## Berkson's Paradox
