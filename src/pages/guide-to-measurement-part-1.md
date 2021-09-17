---
title: "Guide to Web performance Measurements - Part 1"
description: "How do we measure web performance? Whats the best metric to use? What are all the metrics people refer to when measuring different performance aspects."
---

For users of our web apps, it very easy to make a snap judgement on an experience and denounce it slow or hail it as fast. However, as developers trying to intentionally better the experience through the performance lens, we must be able to measure this experience in some way. Measurement helps us distinguish between developer and designer tasks when it comes to enhancing web performance.

People proclaim that first make it work and then make it fast, which is true in most cases. But we've also seen that many times to make something perform better, we need to rework the implementation from scratch. This makes a case for what is notoriously called premature optimization. I believe that premature optimization is not productive if not combined with solid measurements. If we are able to setup performance measurements, there is no reason why we can make it work in a fast manner right from the beginning.

Now, we come to the most important question of this blogpost. What is it that we need to measure to determine the performance of our web app? To that, I'd say, if there was a silver bullet answer, I wouldn't be writing this blogpost.

As referenced in the previous blogpost, one of the reasons web performance is hard is because there is no single metric to tell us this information. It depends on the context, usage and tradeoff acceptable by the app.

## Playground

For the understanding of this blogpost, I created a small and slow website.
Go here to have a look [https://alkaline-classy-microraptor.glitch.me/](https://alkaline-classy-microraptor.glitch.me/)

This website is slow for various reasons. We can work on looking at all the relevant metrics which will help us benchmark any improvement we can make on the site. We will only be looking at the measurements and not working on any improvements in this blogpost.

## At first we had domContentLoaded

I realised some people may not know this, but the window object on your browser has a few useful performance related measurements in it.

Here are the steps to follow

1. Open [https://alkaline-classy-microraptor.glitch.me/](https://alkaline-classy-microraptor.glitch.me/) in incognito mode on a chrome browser (preferably chrome canary)\*
2. Open the devtools console. Here is a tutorial if you can't figure out how to do it.
3. In the console, open `window.performance`. You'll see that this is an object with many keys in it.
4. For our current use case, we are interested in `timings` object and the timingOrigin property.

The `timingOrigin` property is the beginning of the timer. The value is an epoch timestamp and you can look at it more closely in [https://www.epochconverter.com/](https://www.epochconverter.com/)

Next we look at the timestamps in the timings object. They look something like this.
![Screenshot of window.performance.timings object on devtools console](/img/windowtimings.png)

There's so many timing properties in here. Since its Javascript they just show up in alphabetical order. But the actual order can be seen if you arrange the values in ascending order.

Here's a visual I found in the spec for this object that explains the order of the properties that make sense.

![Infographic showing all the metrics aligned in ascending order of their values](/img/navtimings.png)

In the above diagram, we see how pairs of the properties bookend major events in the loading of the web page. We normally use domContentLoadedEventEnd as an indication of how long the page took to load. Also its very easy to point out which events are uncharacteristically slow. For example if responseEnd was what was slowing everything down, then we'd look at CDNs for our html or reduce time in SSR and so on.

## Shift to User Centric Metrics

In practical terms though, these numbers are not very relevant or clear for goal setting or even determining user experience. We needed more "user-centric" metrics.

As defined by the Chrome team, user-centric metrics answer the following questions for the user experience.

1. Is something happening on the page?
2. Is what I'm seeing useful?
3. Has my page finished loading?
4. When can I start interacting with the page?
5. How delightful are my interactions with the page?

I think these questions make a lot of sense about the first page load of the page. Its also important to know the factors on which each of these metrics depend. We will look at each of the factors in detail in future blogposts.

Here are some common metrics that web teams across the world measure to determine and maintain web performance.

### TTFB (Time To First Byte)

This metric very closely answers the question "Is something happening on the page?"
This is the time taken for the page to start receiving the first byte of the HTML.
Things like indiscriminate redirects, http streaming, server time for creating the html are responsible for this metric.

### FP(First paint) or TTR(Time To Render)

This metric answers the question "Is what I'm seeing useful?"
This is the first pass at painting the content onto the webpage by the browser. We may still be waiting for images and other assets but the structure and layout of the page is delivered at this point. This helps the user determine if they are in fact on the right page and that it will help them achieve their goal on that web app.
This metric is affected by the time taken to load the html, javascript and css asset files. It also includes the time taken to parse and evaluate these files. Improving CDN usage and eliminating render blockers are necessary to improve this stage of page load.

### LCP(Largest Contentful Paint)

I'm taking this metric to mean, has the most important thing on this page loaded? This may be text, form, image or even video. This marks the fact that the page is now ready to fulfil its purpose to the user.

Stuff like font loading, images, SSR methods and the use of static pages can make this metric blazing fast.

### TTI(Time To Interactive)

I have to say, this metric is very close to what LCP represents. But, you will see it referred to in many places. It also has a subtle difference. This means that all of the Javascript is loaded and ready to execute. When TTI has not yet been reached, users tend to rage click. Some ways to mitigate this is to reduce the size of Javascript sent through the wire by lazy loading and deferring non critical code.

![Breaking down TTI into its constituents](/img/tti-breakdown.png)

The above diagram is a handy way I try to remember the sequence of these metrics. There are many other metrics in this spectrum but knowing these basic ones will help you place the others on this scale. Remember that these metrics are cumulative. That means that making a fast TTFB affects all the others like a waterfall and reduces overall TTI too. But simply reducing TTI may not affect any other metric.

## Lighthouse and web vitals.

Referring to my [previous blogpost](https://web-perf.dev/web-perf-intro/), on why performance is important, we spoke about how Google wants to calculate search rank based on performance metrics. These metrics are the Core Web Vitals. These are very similar to the ones I spoke of above. But they deserve their own section.

Lighthouse is a tool developed by the Chrome team that initially was a chrome extension but now has been integrated right into the browser developer tools. To open Lighthouse, we must open up the Devtools and navigate to the tab indicating "Lighthouse". We will discuss this tool in depth in the future.

Lighthouse loads up your webpage from scratch on your local browser and provides you with a performance score. This performance score is based off of a number of metrics called (web vitals)[https://web.dev/vitals/].

Three of these webvitals have been chosen as Core web vitals that every webpage must perform well in. These are LCP, FID and CLS. (Three new acronyms for you to learn!)

**LCP** - Largest ContentFul Paint is the same as discussed earlier in the blog.

**FID** - First Input Delay. This one is an interesting metric that is supposed to measure interactivity. How long does it take from the first click on the page to the relative action being performed. I have never actually been able to measure this myself.

**CLS** - Cumulative Layout Shift. This is one of my favorite metrics and kudos to the team for coming up with it. This one refers to the jank in the pages as they load. As in, the whole page shifting when an image or an ad loads. This was something that we were unable to measure using the old navigation api. Again, we'll discuss this one in depth and how to make it green.

## Custom Measurements

The page load metrics may apply to the first load, what if your app was an SPA and you wanted to measure how long a transition takes? Web vitals may not be giving you the right signals. Users may still be complaining even though your Lighthouse score is 90. This is quite possible as the web moves forward rapidly and the metrics are barely keeping up. I saw an instance where a particular select box was loading slowly for a particular user but generally it worked fine. To get to the bottom of it, I had to create special metrics to measure the relevant component only.

In any case, you will see many experienced web professionals recommend that you have your own definition of success and that could mean your own metric. Thankfully there are ways for you to do this.

`window.performance` also has some methods on it for us to use.

I will write more about these methods in Part 2 of this guide. I will also talk about real user monitoring, field metrics and synthetic metrics in Part 2.

To end this blog, here's a little activity you can do on your own and tell me what's the result you get.

Activity steps

1. Goto [https://glitch.com/edit/#!/alkaline-classy-microraptor](https://glitch.com/edit/#!/alkaline-classy-microraptor). This is a tool called glitch, where you can edit the source code of the app. Very much like codesandbox.
2. Click on the project drop down at the top left. This is what it should look like
   ![Screenshot of glitch remix button](/img/glitchremix.png)
3. Click on the "Remix this project" button. This creates your very own fork of the project.
4. Now navigate to the code base and start editing index.html.
5. In there, add the following code.

```jsx
// Add this to line 11
<script type='text/javascript'>
	window.performance.mark('TTI:start')
</script>

// Add this after main script is run possibly line 74
<script type="text/javascript">
  window.performance.mark('TTI:end')
  window.performance.measure('TTI', 'TTI:start', 'TTI:end')
</script>
```

6. Now save the project, open the webpage in a new tab.
7. Open the devtools console.
8. Type in `window.performance.getEntries()`. Do you see the new TTI measure and the two start and end marks there? If so what are their values? Send them to me and we can discuss how exactly performance differs on different circumstances!

Reply back to my newsletter with the answer or you can also DM me at [@PKodmad](https://twitter.com/PKodmad) on twitter.

I hope you enjoyed Part 1 of this Guide to Measurements. Stay tuned for Part 2.

_Footnote_ -

- - We try to make our measurements in the cleanest and latest environment as possible. The incognito mode doesnt have chrome extensions which tend to compete for resources and Chrome browser's canary version has all the latest features we can expect our users to be experiencing in the near future.
