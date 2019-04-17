---
layout: post
title: Volatility - Expectations vs Reality
image: /img/regression1.jpg
---

In this post we're going to take a look at the relationship between the stock index, and the volatility index.  Stock indices are familiar to most people: the weighted average price of shares in a sector or region.  Volatility indices are less in the public consciousness. They represent the market expectations of how much a given index will move over the next month.  Or so they claim to!

### Some analysis....

Two of the biggest regional indices are the Eurostoxx 50 and the S&P 500.  Their respective volatility indices are the V2X and the VIX.  We analyse their daily moves over a sixteen year period.

![](/img/regression1.jpg)

In this first plot we can see a high negative correlation, which is what you would expect.  As markets go down, moves become larger as fear manifests itself faster than greed.

![](/img/rolling_corr.jpg)

The second plot shows that correlation varies over time, and tends to be higher in times of market stress.

![](/img/expvsreal.jpg)

In the third plot you see the expected market volatility is almost always higher than the reality.  Why is this?  The main driver is that the value of these volatility indices is determined by the market pricing for options.  People buy options to hedge their portfolios.  They buy this as 'insurance', in case the bad times start rolling.  And much like real world insurance they will need to pay more than fair value to get their protection.  

So we can see that these volatility indices are less a guess on market moves in the coming month than a 'fear index' of the potential pitfalls ahead.
