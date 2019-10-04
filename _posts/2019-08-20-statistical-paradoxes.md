---
layout: post
title: Statistical Paradoxes
categories: [misc, tech]
comments: true
---

While reading Judea Pearl's [The Book of Why](http://bayes.cs.ucla.edu/WHY/), I digged a bit more into the statistical paradoxes and decided to sum the most interesting ones here. They work as brain-teasers and eye-openers, and it would be helpful to integrate them into our daily critical thinking and decision-making processes.


## Monty Hall Problem

Assume that you're on a game, and there are three doors upon you where one leads to a car and the others lead to goats. You pick the door #1, and the host opens one of the goat-doors. Now, you can either stay on your door, or switch your door. What would you do?

![](/images/monty.jpg "Monty Hall")

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

Now, suppose we are to pick the most ideal goat, which we define as fluffy and kind (not stubborn). We visit the barn and there are 100 goats, 40 are fluffy (40%), 10 are kind (10%), 5 are fluffy and kind ( 12.5% of the fluffy ones are kind). The owner of the goats shows us 45 goats, which are fluffy and/or kind. Among the ones we see, over 22% of them are kind (10/45), and again 12.5% of the fluffy ones are kind. The kindness increased but fluffiness remained same. Hence, people usually tend to think that these two attributes are negatively correlated: if a goat is fluffy, it is a small chance that it is kind, and vice versa. This occurs due to selective bias and observing only the fluffy or kind goats. 

![](/images/goat.jpg "Berkson's")

|           | Kind | Not kind 
| ---       | ---  | ----- 
| Fluffy    | 5    | 35 
| Not fluffy| 5    | 55 

P( kind ) = 10/100 (10%) <  P( kind | fluffy) = 5/40 (12.5%)
P( kind | kind U fluffy) = 10/45 (22%) >  P( kind| fluffy, fluffy U kind) = 5/40 (12.5%)

Here, among the fluffy and kind ones, the percentage of kind ones is less if we know it is fluffy. However, in the overall population, the percentage of kinds is actually high if we know it is fluffy. 

Such paradox also occur in epidemiological studies concerning two independent diseases. Usually, the researchers sample the people from disease 1 and/or disease 2, and they tend to think that these diseases are negatively correlated. That occurs because they do not include undiseased (not 1, not 2) samples.

Likewise, people tend to look for nice and handsome partners. It might feel like these features are negatively correlated, because they do not consider the both unkind and ugly ones.


## Simpson's Paradox

Let's assume that there is a drug that increases the heart attack rate both male goats and female goats. Yet surprisingly, it decreases the heart attach in whole population. 

The figure shows this phenomenon (from Pearl's book): 

![](/images/simpsons.png "Simpson's")

P(heart attack in women | drug) = 3/40 > 1/20 = P(heart attack in women | no drug)
P(heart attack in men | drug) = 8/20 > 12/40 = P(heart attack in men | no drug)
P(heart attack | drug) = (3+8)/(40+20) < (1+12)/(40+20) = P(heart attack | no drug)

When we combine the probabilities among women and men, inequality reverses direction. For an unknown reason, people get surprised on this reversal even though it is mathematically normal. 

A/B > a/b
C/D > c/d
(A+C)/(B+D)>(a+c)/(b+d) -> Such a combination is actually wrong!

This fallacy also occurs in college admissions, or in sport's competitions...

## Gambler's Fallacy

## Friendship Paradox

$$
\begin{aligned}
\mathbf{x} &= [x_1, x_2, \dots, x_n] \\
\mathbf{y} &= [y_1, y_2, \dots, y_m]
\end{aligned}
$$






