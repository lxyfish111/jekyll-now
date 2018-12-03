---
layout: post
category: ""
title:  "Compute Karl Pearson’s coefficient of correlation"
tags: [ML]
---

Correlation and Regression Lines - A Quick Recap #1

Here are the test scores of 10 students in physics and history:

Physics Scores  15  12  8   8   7   7   7   6   5   3
History Scores  10  25  17  11  13  17  20  13  9   15
Compute Karl Pearson’s coefficient of correlation between these scores. 
Compute the answer correct to three decimal places.

Output Format

In the text box, using the language of your choice, print the floating point/decimal value required. Do not leave any leading or trailing spaces.

For example, if your answer is 0.255. In python you can print using

print("0.255")
This is NOT the actual answer - just the format in which you should provide your answer.

``

import math

def calcMean(x,y):
    sum_x = sum(x)
    sum_y = sum(y)
    n = len(x)
    x_mean = float(sum_x+0.0)/n
    y_mean = float(sum_y+0.0)/n
    return x_mean,y_mean

def calcPearson(x,y):
    x_mean,y_mean = calcMean(x,y)
    n = len(x)
    sumTop = 0.0
    sumBottom = 0.0
    x_pow = 0.0
    y_pow = 0.0
    for i in range(n):
        sumTop += (x[i]-x_mean)*(y[i]-y_mean)
    for i in range(n):
        x_pow += math.pow(x[i]-x_mean,2)
    for i in range(n):
        y_pow += math.pow(y[i]-y_mean,2)
    sumBottom = math.sqrt(x_pow*y_pow)
    p = sumTop/sumBottom
    return p

physcr = input().split()
hisscr = input().split()

physcr = list(map(int, physcr))
hisscr = list(map(int, hisscr))

print (physcr)
print (hisscr)

``