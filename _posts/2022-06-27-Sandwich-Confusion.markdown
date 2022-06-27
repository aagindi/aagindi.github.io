---
layout: post
title:  "Sandwich Confusion"
date:   2022-06-27 10:17:04
categories: post
---

*Note: This is the sample project for the 2nd 2022 Tech Connect Coding Challenge, the code can be found [here](https://github.com/agindi/TCHotTakes_SampleProject) and the template is [here](https://github.com/agindi/TCHotTakes_Template)*

### The Question
Does time spent at M&T lead to sandwich confusion? Scanning over the dataset, an alarming number of respondents seem to suffer from **Sandwich Confusion**, which I define as holding one of the two opinions:

1. A hotdog is a sandwich.
2. If you fold a pizza, it becomes a sandwich.

I will not even begin to discuss why these are both so obviously wrong. I believe it falls on M&T Bank as an institution to help it's employees overcome this character flaw, so I will compare the rates of sandwich confusion with time spent at the bank in order to gauge our effectiveness.

### The Numbers
First, I tried to quantify the degree of sandwich confusion with a `sandwich_confusion_score`, which is 0 if a respondent was correct on both counts, 1 if they got one of the questions incorrect, and 2 if both were incorrect. I also estimated time at the bank using the respondents cohort. They received a value of 0 if they are an intern, 1 if a 2021 TDP, and 2 if a 2020 TDP. A correlational analysis of the relevant values comes out like this:

```
                          hotdog_correct  pizza_fold_correct  sandwich_confusion_score  time_at_bank
hotdog_correct                  1.000000            0.284282                 -0.884395      0.128477
pizza_fold_correct              0.284282            1.000000                 -0.698899      0.134233
sandwich_confusion_score       -0.884395           -0.698899                  1.000000     -0.161191
time_at_bank                    0.128477            0.134233                 -0.161191      1.000000
```

The most important value here is the correlation between time_at_bank and sandwich_confusion_score, which is **-0.161191**. This slight negative correlation means that as time at the bank increases, there is a very slight decrease in sandwich confusion. This effect seems to be very weak, but perhaps a visual aid will help us decide if further action really needs to be taken to address this problem.

![Sandwich Confusion vs. Time Spent at M&T](/assets/images/TCHotTakes/Figure_1.png)

Interestingly, there seems to be some benefit to spending some amount of time here at M&T, as the rate of overall confusion decreases from *New* to *1 Year*. However, it is clear that the poor souls that are not completely cured of their sandwich confusion may regress back into extreme confusion over time.

### Policy Recommendations
Sandwich confusion is a real problem. Truly, it is. M&T should utilize it's existing learning and development infrastructure to enable bank personnel to educate themselves. I'm talking Lunch 'n Learns highlighting sandwich construction, required Workday trainings reviewing sandwich history and etymology. Finally, incentives like High Fives and Bills Tickets should be offered to employees who go above and beyond in spreading the correct sandwich outlooks. Hopefully, with these policies implenented, M&T will be launched into a bright future filled with innovation, agility, and indisputable sandwich insight.