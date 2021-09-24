---
title: "Guide to Web performance Measurements - Part 2"
description: "What is Real User Monitoring?"
---

### Breaking down the measurement exercise.

In [Part 1](https://web-perf.dev/guide-to-measurement-part-1/), we discussed the basics of user centric metrics and web vitals. We ended the post with a small exercise on measurement. Many of you messaged me with the values you got and it was delightful. Thanks a lot for the replies.

This exercise was just to demonstrate how easy it is to define your own custom measurement. Let's break it down further.

`window.performance.mark(<name>)` - This function creates a timestamp mark at the point where it was invoked. So that means, you can collect this mark later at a time when your browser is idle and be sure that the timestamp is relevant and accurate to your needs.

`window.performance.measure(<name>, <mark1>, <mark2>)` - This function measures the difference between the timestamps in the two marks we provided and stores it under the given name. While I agree we could do it manually, timestamps are notoriously tricky when least expected and I think it's better to use the provided API for these purposes. Try to make sure that the measure you chose has a real world meaning to you and your team. Just subtracting between different marks will not help your cause of performance improvement unless it really means something for your web app.
In the exercise, we measured the time taken by the browser to execute html instructions from 1 point to another and called it TTI. Effectively you can measure any intervals using these methods. Have an annoying chart taking too long to load? You can add a mark at the beginning and end of the load of that component. I find this pretty flexible and we found a lot of value in the dev teams I've worked in.

`window.performance.getEntries()` , `window.performance.getEntriesByType(<type>)`, `window.performance.getEntriesByName(<name>)` - Any mark or measure we create gets dumped into the same "entries" object that the browser also dumps a ton of measurements into. When you look at the output of `getEntries` that can get a little overwhelming depending on how much time has been spent navigating around the website. For example, for every resource fetched, the performance measure is calculated and filled as an entry. We can of course manually filter for the values we want, but the browser provides some filtering api. `getEntriesByType(<type>)` will get us only the entries of that type. `mark` is a type and `measure` is also a type. The other entry types are listed here [https://developer.mozilla.org/en-US/docs/Web/API/PerformanceEntry/entryType](https://developer.mozilla.org/en-US/docs/Web/API/PerformanceEntry/entryType)

It's very common for developers to create marks and measures at various points in the website and send it over to an analytics server. Then we perform statistical analysis on these measures, track them and report over them for a consistent view of our web app's performance.

Feel free to poke around all the performance entries in your website and see if anything jumps out for you. We will talk more about resource, paint and long task timings in the future.

## Real User Monitoring

Lovingly called RUM, this is pretty much industry standard for large scale web apps to measure performance, prioritise and make decisions on what fix to make, or to decide if performance regressed and so on. A real user is a person using your web application for their needs under unknown circumstances. Metrics gathered from a real user are also called Field metrics. Metrics gathered in our local machines or in synthetic environments is called Lab metrics. This naming is not universal. Some people call it synthetic metrics also. The distinction is what I want you to focus on.

Almost all the time, the discrepancy between lab and field metrics is kind of mind boggling. Many times my team would identify a performance problem as pointed by field metrics, replicate it in lab scenarios, fix and see great improvement in lab metrics only to see it flop in field metrics.

So lets dive right into the differences in lab and field metrics.

### Environment

Lab metrics can sometimes mean locally or CI run cypress tests,Webpagetest(Coming up in a future blogpost) or speedcurve running our webapp in different locations and conditions. A custom setup where the environment is very close to production, accounting for geography etc.
It is pretty rare that the user environment of production is mimic'd in lab conditions. Its expensive and must justify the investment in terms of resources to make it happen.

### Uniformity of data points

In Lab metrics, we typically know the volume of data gathered given a particular day or time. If one of your tests runs every 5 mins in 5 locations then we know that everyday there are 288 data points per location. However, in the field everything is random. Users only use your app when convenient to them. For eg in Jira, we saw a huge spike in requests every Monday during standups. These spikes varied in time and size based on when Monday was in different locations. Similarly, we had very low usage of our app on the weekends (which is good!) but it skewed up our data points and other numbers heavily. Even if we decided to account for weekends, sudden holidays in certain location would also make our usage volatile. When the number of data points change so much, the pressure on the underlying systems also change accordingly. Hence, we must use to statistics and percentiles.

**Percentile distribution**

Say you are measuring the TTI of your web application. In 3000 page view hits, you have gathered varying TTI data for different users. To make sense of this, our first instinct is to take an average. However the average completely demolishes the underlying meaning of the data and gives you a number that is indicative of nothing. If you have even one value that is comparitively enormous, it brings your average up significantly.
Instead, what we do, is sort all the data points in increasing order. We then mark the mid point of this sorted list. The TTI value there is the median or the 50th percentile. This means all the values below it are less than or equal to that value.
Lets look at an example of how these 7 data points could be analysed.

![avg vs mean.png](/img/avgvsmean.png)

We see that the median value is the one that makes the most sense. 50% of the values are below or equal to this and 50% are above this value. This means 50% of your users are facing high TTI values above 2 sec. Typically, companies tend to use the 90th percentile or P90. This targets the painpoint of 10% of your users experiencing the worst problems. As you solve their problems, we see the p90 drop and thus provide performance value.
So next time you see a **P value,** you will know what it means.

### Data differences and User flows

The next main difference between lab and field metrics is that lab metrics can almost never accurately mimic the data created by the user or the exact user flows experienced. This is because each user is unique and they form new ways of using your software. Only with extensive tracing and user research, you will be able to match lab flows with the actual user flows. For large products, when users complain that things are slow, its really hard to pinpoint which user flow they are talking about. So many times metrics fail to represent users too. We need to pick metrics that represent the majority of our user problems.

Because of all these issues, Real User Monitoring or RUM is necessary for a web app of a decent size. We record the web vitals or any custom metrics on the user machine as they are using our app, send this data anonymously to our Observability setup, account for different loads on different days, find the P90 and compare it to the previous interval's value. A most common chart in large orgs is P90 value of TTI every single day. If the value goes up on a particular day (regardless of lab metrics), then that is counted as a regression and debugged for the root cause.

## Summary

So thats the end of my guide to performance measurement. In this two part series, we learned about

1.  The OG metrics `window.performance.timings` and `domContentLoaded` event.
2.  User-centric metrics became more popular and led to the rise of web vitals. Core web vitals are current industry standard of metrics.
3.  Web vitals is a single tool which might not be relevant for all web apps. So there is a need for custom metrics.
4.  We create custom marks and measures using `window.performance.mark(<name>)` and retrieve it using `window.performance.getEntriesByName(<name>)`
5.  Real user monitoring is the only way to truly understand and care for your user's experience with regards to performance. Lab metrics give a good signal but are ultimately overridden by field metrics.

I hope this gives you a good idea about how much and what you need to measure as per your web app's needs. tl;dr - If it's a static website/blog site, web vitals and webpagetest should be good enough to guarantee good performance. If its a SAAS application with a scaling user set, you might consider investing in custom metrics and real user monitoring services.
