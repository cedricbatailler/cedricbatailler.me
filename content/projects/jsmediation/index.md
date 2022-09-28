---
title: JSmediation
summary: |
  In social science, statistical mediation models are a popular method to show
  causality. The `JSmediation` package implements mediation models in an 
  easy-to-use R package.
date: 2019-06-13
categories:
  - R package

links:
  - icon: link
    icon_pack: fas
    name: docs
    url: https://jsmediation.cedricbatailler.me/
  - icon: github
    icon_pack: fab
    name: source
    url: https://github.com/cedricbatailler/jsmediation
---

# Showing causality

Effects things have on other are sometimes indirect. Let's take an example
invoving a soccer ball and a broken glass. Sometimes, you will break a glass by
shooting the ball right onto it. But sometimes not. Sometimes, the ball will
land next to a cat, a cat peacefully sleeping on someone's laps. Sometimes, the 
cat will ended up scared which will result in a jump right onto a table, table 
on which was the glass[^1]. Indirect.

And, sometimes, it is important to investigate how these indirect effets are 
chained. It is known that people are less likely to buy drugs with complex 
name[^2]. But why? What is the chain behind this effect?

**Mediation analysis** is a statistical tool that can be use to find out that 
the reason why people are less likely to buy drug with complex name is because 
they percieved the drugs as more dangerous. While there is several way to
conduct mediation analysis, the `JSmediation` package implements the best[^3] 
of them: joint-significance.

Have a look at [the documentation](https://jsmediation.cedricbatailler.me/) and 
give it a shot!

[^1]: Yeah. It happens.

[^2]: Dohle, S., & Siegrist, M. (2014). Fluency of pharmaceutical drug names predicts perceived hazardousness, assumed side effects and willingness to buy. _Journal of Health Psychology_, _19_(10), 1241-1249. doi: 10.1177/1359105313488974

[^3]: This has to be understood as the one with the lowest number of false positive. Yzerbyt, V., Muller, D., **Batailler, C.**, & Judd, C. M. (2018). New recommendations for testing indirect effects in medi‑ational models: The need to report and test component paths. _Journal of Personality and Social Psychology_, _115_(6), 929–943. 10.1037/pspa0000132