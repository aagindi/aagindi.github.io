---
layout: post
title:  "Slay the Spire: Apotheosis and Musings"
date:   2023-05-16 6:02:20
categories: problem
---

### Introduction
Slay the Spire is a roguelike deck-building video game released in 2017. I lack the context, experience, and patience to explain exactly exactly that that means, but I will shamelessly state that I am a big fan and (with slightly more [shame](https://steamtime.info/stats)) boast my Steam Stats at time of writing:

!["Progress"](/assets/images/sts-math-and-musings/progress.png)

I consider myself decent - at this point I play mostly at the highest difficulties, and lose the vast majority of attempts. What keeps me coming back is despite knowing and having a decent grasp on nearly every individual aspect (encounters, cards, and relics) that this game has to offer, each run accumulates different context than the last. At every decision point, the optimal choice both depends on and may alter the context, in big ways and small. I also have some optimizer tendencies, and STS is a game of potentially limitless number crunching. There are fairly trivial questions, like "how much damage can I do with the cards in my hand?" to the slightly more advanced "what is the probability that I draw lethal damage next turn?" to the highly advanced "what is the expected outcome of a given encounter?". The first requires mostly addition, with some potential multiplication depending on status effects. The second requires computing the damage for all possible hands that you can draw next turn, and weighing the probability of each outcome. The final question requires understanding the interplay between cards that may or may not be drawn together, accounting for the likelihood of "bottom decking" the most important cards, and budgeting enough resource (energy, card draws, potions, health) to adequately deal with the enemy intents.

The first two questions are easy enough to calculate - they just require a good deal of mental effort and a decent understanding of probability. However, the final question is harder. If there was an effective way to answer it in all situations, the game would essentially be solved. I enjoy thinking about questions that are more complex than the first two, but less complex than the final one. It is a space with concrete questions can have interesting answers, shedding new light on the relative value of certain resources. Often, these questions ask about a more concrete sub-goal, like "how quickly can I play a certain important card?". This is the question I will explore below, and for the sake of the discussion I will be thinking about the card Apotheosis, which upgrades all other cards in the deck. This is one of the most powerful cards in the game, and when it is in a deck, playing it as quickly as possible is the first goal in most fights. I will consider this question in various contexts, and compare the answers to gain some deeper insights.

### 1. The Ironclad Starter Deck
Suppose a new run has just begun, and the great land whale Neow, after resurrecting you - the last of the Ironclad tribe - for the umpteenth time, generously gives you an Apotheosis. Yes, the lore of this game is strange, and yes, this is a possible way to start the game. The deck now consists of 11 cards - 5x Strike, 4x Defend, 1 Bash, and 1 Apotheosis. Going into the first combat, what is the expected turn that you can draw and play Apotheosis?

This depends on the card's position in the draw pile, which is random (uniformly distributed). Let $$A$$ be a random variable representing the position of the Apotheosis, $$A \sim Unif([1,13])$$. Let $$t(x)$$ be the turn you can play Apotheosis if it is in the $$x$$th position.

$$ t(x) := \lfloor \frac{x}{5} \rfloor + 1 $$

This identity results from the fact that you draw 5 cards per turn. Intuitively, if it is in the first five cards (0-indexed), $$t(x)=1$$, if it is in the next 5 cards, $$t(x)=2$$, and if it is on the bottom, $$t(x)=3$$. The expectation follows trivially:

$$ E[t(A)] = \sum_A p(A) t(A) = \frac{1}{11}(5 \cdot 1 + 5 \cdot 2 + 1 \cdot 3) = \frac{18}{11} \approx 1.6 $$

There are a few things to note when interpreting this result. The first is that it often doesn't work to maximize the average case, because often the worst case comes to pass. If the worst case is game losing, then you're going to run into trouble eventually. Second, this is a very simple example with very little complicating context, so the fact that it matches intuition so closely is not shocking. $$E[t(x)]$$ would mean that half the time we would get it turn 1, and the other half would be turn 2. Since we have 11 cards instead of ten, the expected turn is very slightly more than that. Let's try it with additional context.

### 2. The Silent in the Exordium
The exordium is the second act of this game, a whole new set of encounters and challenges that await the player after the first boss. At this point, the player will have added many cards, including in this case an Apotheosis - let the deck size be $$n$$. The class in this example, the Silent, has one major distinction with the Ironclad from the previous example - she is allowed to draw 7 cards on her first turn instead of five. As a function of $$n$$, what turn can she draw and play the Apotheosis. (Assuming that none of the added cards allow for more draw on that turn).

Again, let $$A$$ be a random variable representing the position of the Apotheosis. $$t(x)$$ needs to be defined differently to account for the bonus turn 1 draws:

$$t(x) := \begin{cases}
1 & x \in {0, 1}\\
\lfloor \frac{x - 2}{5} \rfloor + 1 & otherwise
\end{cases}
$$

The expectation is again, relatively straightforward:

$$ E[t(A)] = \sum_A p(A) t(A) = \frac{1}{n} \sum_A t(A) $$

Insights from this data are again fairly common sense - comparing this to the previous section (at $$n=11$$) we see that the Silent is expected to be able to draw and play the Apotheosis faster than the Ironclad due to the bonus draws on turn 1. Also important to note is that there is a positive relationship between $$n$$ and $$t(A)$$ - the more cards in your deck, the longer it will take (on average) to play the most important card. Also, the worst case scenario becomes even worse.

### 3. Ring of the Snake
Silent's starting relic - which gives her the ability to draw two extra cards turn one - is called The Ring of the Serpent. However, after defeating a boss, she is sometimes offered the choice to exchange this (very strong) relic for The Ring of the Snake, which allows her to draw six cards every turn (instead of five). Suppose our Silent from the previous example has the Ring of the Snake instead of the Ring of the Serpent. How does that change our expectation of when we can play the Apotheosis?

$$ t(x) := \lfloor \frac{x}{6} \rfloor + 1 $$

10: 1.300000... 1.400000
11: 1.363636... 1.454545
12: 1.416667... 1.500000
13: 1.538462... 1.615385
14: 1.642857... 1.714286
15: 1.733333... 1.800000
16: 1.812500... 1.875000
17: 1.882353... 1.941176
18: 2.000000... 2.000000
19: 2.105263... 2.105263
20: 2.200000... 2.200000
21: 2.285714... 2.285714
22: 2.363636... 2.363636
23: 2.478261... 2.434783
24: 2.583333... 2.500000

So, which of the two rings helps our Silent play her Apotheosis faster? As with many questions, this depends on *context*, in this case the number of cards in the deck. If there are fewer than 18 cards, Ring of the Serpent is better. If there are more than 22, than Ring of the Snake is better. These cut-offs emerges clearly in the data and may seem arbitrary at first, but they can be explained; within the first three turns, the Ring of the Snake is able to draw 18 cards, and the Ring of the Serpent is able to draw 17. Within the first four, the Ring of the Snake is able to draw 24 cards, and the Ring of the Serpent is only able to draw 22.

### 4. Defect with Toxic Egg
The Defect, our robot friend, is the third playable character in STS and - in my opinion - the most complicated. In this example, they are wielding a deck of 25 cards (including an Apotheosis, and once again no cards that draw other cards) as well as a Toxic Egg, which means that any skills they add to the deck will be upgraded. They have just barely survived a fight with a leaping avocado and a rat with some kind of fungus growing out of it, and are offered a card reward with the following options: Seek+, Skim+, and Machine Learning.

Seek+ allows the player to add two cards from the draw pile directly to their hand, Skim+ allows the player to draw the next four cards from their draw file, and Machine Learning, once played, allows the player to draw six cards per turn instead of five. Which of these options will allow our Defect to draw and play the Apotheosis the fastest? The answer may seem obvious, but let's crunch the numbers anyway.

#### 4a. Seek+
Whenever we draw Seek+, we can use it to draw and play Apotheosis if we have not done so already. So, let $$A$$ be a random variable representing the location of the Apotheosis, and let $$S$$ be the position of our Seek+ card. They cannot be equal, so we describe the joint probability mass function as such:

$$ p(a,s) = \begin{cases}
0 & a = s \\
\frac{1}{n^2 - n} & otherwise
\end{cases}$$

Note that since we're supposing $$n = 25$$, we have the non-zero probabilities as $$\frac{1}{600}$$. We can now define $$t(x)$$ and the expectation as follows:

$$t(a, s) = \lfloor \frac{min(a, s)}{5} \rfloor + 1$$

$$E[t(A, S)] = \sum_{a \in A, s \in S} p(a, s) t(a, s) = \frac{1}{600} \cdot \sum_{a \in A, s \in S: a \neq s} \lfloor \frac{min(a, s)}{5} \rfloor $$

Instead of computing this by hand, I'll employ some python.

```
import math

def t_seek(a, s):
    return math.floor(min(a, s) / 5) + 1

def p_seek(a, s, n):
    if a == s:
        return 0
    return 1 / (n**2 - n)

def e_seek(n):
    sum = 0
    for i in range(0, n):
        for j in range(0, n):
            sum = sum + p_seek(i, j, n) * t_seek(i, j)
    return sum

e_seek(25) # returns 2.166666666666663
```

#### 4b. Skim+
When we draw Skim+, we can use it to draw the next four cards, and if we draw Apotheosis, we can play it. We will use the same joint probability function as above with Seek, but the logic for our function $$t(a,s)$$ is more involved.

$$ t(a,s) := \begin{cases}
\lfloor \frac{s}{5} \rfloor + 1 & s < a \land 5 \cdot \lceil \frac{s}{5}\rceil + 4 \ge a  \\
\lfloor \frac{a - 4}{5} \rfloor + 1 & s < a \land 5 \cdot \lceil \frac{s}{5}\rceil + 4 < a  \\
\lfloor \frac{a}{5} \rfloor + 1 & otherwise \\
\end{cases}$$

The first case is when the skim is before the apotheosis and the apotheosis is in a place where it can be drawn by skim. $$s$$ must be increased to the nearest multiple of five before the four additional cards are added to account for it's variable position within the five cards that are drawn regardless. The second case occurs when the skim+ is first but the apotheosis cannot be reached; we assume that we play the skim+ regardless to help us peer deeper into the draw pile, which is why there is an $$a - 4$$ term - it's as if there's four fewer cards before the apotheosis.

Once again, instead of computing by hand, I'll turn to python.
```
import math

def t_skim(a, s):
    if s < a & (5 * math.ceil(s / 5)) + 3 >= a:
        return math.floor(s / 5) + 1
    elif s < a & (5 * math.ceil(s / 5)) + 3 < a:
        return math.floor((a-4) / 5) + 1
    return math.floor(a / 5) + 1

def p_skim(a, s, n):
    if a == s:
        return 0
    return 1 / (n**2 - n)

def e_skim(n):
    sum = 0
    for i in range(0, n):
        for j in range(0, n):
            sum = sum + p_skim(i, j, n) * t_skim(i, j)
    return sum

e_skim(25) # returns 2.819999999999991
```

#### 4c. Machine Learning
Machine Learning feels like the clear loser when it comes to playing the Apotheosis as quickly as possible, but again, let's check the computation. If we draw the Machine Learning before the Apotheosis, it will help us play it faster by giving us an extra draw at the start of every turn. We can model this affect as shown:

$$ t(a,m) := \begin{cases}
\lfloor \frac{a}{5} \rfloor + 1 & a \le 5 \cdot \lceil \frac{m}{5} \rceil - 1 \\
\lfloor \frac{a - (5 \cdot \lceil \frac{m}{5} \rceil) - 1}{6} \rfloor + \lceil \frac{m}{5} \rceil + 1 & otherwise
\end{cases}$$

Breaking this down, we can interpret $$\lceil \frac{m}{5} \rceil$$ as "the turn that we play the Machine Learning". The first case occurs when the Apotheosis is either before or drawn at the same time as the Machine Learning. If the Apotheosis is drawn after the Machine Learning, the resulting expression for the turn we play it becomes much more strange. $$5 \cdot \lceil \frac{m}{5} \rceil$$ expresses the amount cards that were drawn prior to the turn where Machine Learning was played. This subtraction is balanced by adding the number of turns that the machine learning was not in play outside the floor brackets. All of this extra work is merely to draw an extra card each turn (i.e. change denominator from five to six).

Yet again, here's some code.
```
import math

def t_ml(a, m):
    if a <= (5 * math.ceil(m / 5)) - 1:
        return math.floor(a / 5) + 1
    return math.floor((a - 5 * math.ceil(m / 5) - 1) / 6) + math.ceil(m / 5) + 1

def p_ml(a, m, n):
    if a == m:
        return 0
    return 1 / (n**2 - n)

def e_ml(n):
    sum = 0
    for i in range(0, n):
        for j in range(0, n):
            sum = sum + p_ml(i, j, n) * t_ml(i, j)
    return sum

e_ml(25) # returns 2.8166666666666575
```