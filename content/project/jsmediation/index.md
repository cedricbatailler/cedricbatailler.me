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

# Showing causality with maths

Effects things have on other are sometimes indirect. Sometimes, you will break
a glass by shooting a soccer ball because it will land next to a cat sleeping
on someone's laps who will jump on the table because it is scared[^1].

And, sometimes, it is important ton investigate how these indirect effets are 
chained. For example, it is known that people are less likely to buy drugs with 
complex name[^2]. But why?

**Mediation analysis** is a statistical tool that can be use to find out that 
the reason why people are less likely to buy drug with complex name is because 
they have a higher percieved hazardousness. While there is several way to
conduct mediation analysis, the `JSmediation` package implements the best[^3] 
of them: joint-significance.

Have a look at [the documentation](https://jsmediation.cedricbatailler.me/) and 
give it a shot!

[^1]: Yeah. It happens.

[^2]: Dohle, S., & Siegrist, M. (2014). Fluency of pharmaceutical drug names predicts perceived hazardousness, assumed side effects and willingness to buy. _Journal of Health Psychology_, _19_(10), 1241-1249. doi: 10.1177/1359105313488974

[^3]: This has to be understood as the one with the lowest number of false positive. Yzerbyt, V., Muller, D., **Batailler, C.**, & Judd, C. M. (2018). New recommendations for testing indirect effects in medi‑ational models: The need to report and test component paths. Journal of Personality and Social Psychology, 115(6), 929–943. 10.1037/pspa0000132