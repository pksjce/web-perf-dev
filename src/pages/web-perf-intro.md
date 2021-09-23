---
title: "Why is web performance so hard?"
description: "What do I mean by web performance, why is it important and hard to get right?"
---

### Understanding web performance basics

#### Client side web development

Web application development can broadly be classified into server-side and client-side work.

On the client side, we tend to work with Javascript, HTML and CSS. We may also be using higher abstractions, languages like Clojurescript, type aware languages like Typescript, frameworks like React or Vue. Even higher order setups like NextJS, GatsbyJS maybe employed. These all however boil down to the same three primitive technologies(JS/HTML/CSS ) as thats what it gets converted to, to actually run in browsers for our users.

#### Browsers effect on performance

Browsers are the primary environment for our client side code. Other applications like electronjs, react native etc may also loosely work on the same principle. In this workshop, we will concentrate on desktop and mobile browsers. These are important because, the implementations for browser engines that run Javascript et all can be subtly different on them. Especially on older browsers, brand specific mobile browsers etc. These can affect performance many times. For eg, Chromium supports some optimisations with script loading priorities that aren't supported on firefox. As we move ahead with new standards and language features, we will see staggered adoption on different browsers which may cause performance descrepancies. However, we assume that the bulk of our users are on a Chromium based browser. If they insist on using a different browser, then challenges will arise in debugging and fixing performance issues there.

#### Network bandwidth

The most obvious aspect of web usage we are worried about is network bandwidth. These can vary from user to user, location, circumstance etc. This is one of the primary limiter for any web app or page. While we really can't do much about network bandwidth for our users, we need to be aware of how our web app behaves under different bandwidth conditions. This is where techniques of progressive enhancement and feature limiting based on bandwidth considerations can come into play. We start to think about the critical path of rendering our web app and try to defer everything else. We must test our web apps under different bandwidth circumstances to see that it holds well in all aspects including performance.

#### CPU capacity

Finally, we need to acknowledge machine capacity or computer CPU of various devices capable of running browsers which in turn load the web app you have developed. Again, this is a limiter that we cannot influence much. But this awareness is important because as developers, we can have uber fast high-end machines, but as users, they maybe on a 2016 asus phone that really doesn't have gigabytes of RAM.

#### Combining these factors

Now, web performance is the behaviour of your web app with regards to how fast it responds to user requests under different combinations of above circumstances. The trick is to consider worst cases for all the above cases. Sometimes a user maybe forced by their employer to use a certain browser version, or they may not be able to afford machines with high CPU performance or they may be running too many apps on their machine to allocate enough memory to your web app. It may also be that they may just be on an airplane or on a subway line trying to read the contents of your site. This is where it pays to know your users better and design their experience with performance in mind.

#### Note on percieved performance

Percieved performance is a crucial factor in user satisfaction. It is based on how engaged users feel with your site. It may be at the beginning of page load or it may be during other critical interactions. You can make the user feel as if the site was fast without actually making improvements in raw speed. This also tends to be a design problem in your applications. Delightful applications usually have high percieved performance.

#### Defining web performance

Web Performance, here, is an umbrella term that covers many behaviours such as

1. How fast does your page load any content on first request of url?
2. How long does it take before the page is ready for the user to interact with?
3. How does it feel to the user as they use various components of your page?

### Importance of web performance

Truth is, we've been working on performance engineering on the client side for quite some time. It should no longer require any justification to spend time and resources on this. Performance is a feature. Having a performance problem is a bug that needs to be fixed. Yet time and time again, we see performance issues in sites as users. We see slow loading, slow interaction and we also hear of teams that don't reward performance and accessibility work.
So, here is a small section on why performance is important esp in this day and age.

#### Google web vitals

The biggest driver of performance work on browsers right now is the Google Chrome team. They define new metrics, build new tools, create content and educate developers on performance all the time. Their current most impactful metrics are the [core web vitals](https://web.dev/vitals/). We'll discuss these in a future blogpost. For now it's sufficient to know that these are measure that websites can use to gauge how their users are experiencing the sites.
An important update to these "vital" measures is that they will affect [search ranking of websites](https://developers.google.com/search/docs/advanced/experience/page-experience) on the google search engine. While there is sceptisim that its a [hype train](https://sylormiller.com/posts/2020/core-web-vitals/) that might cause developers to fixate on these metrics alone, there is also positives such as increased budget for performance work on dev teams.

#### Research and Case Studies

As I mentioned on the landing page of [web-perf.dev](https://web-perf.dev/) , it's proven through user research that [slow websites are stressful to users](https://www.netimperative.com/2020/12/09/blood-pressure-study-which-website-issue-cause-users-the-most-stress/).

![Image from above study link](/img/slow-stress.png)

On the other hand, there are plenty of case studies that prove, comprehensively, that improving client side performance improves user satisfaction, sales and even opens up new markets. Perusing [WPO stats](https://wpostats.com/) is inspiring and has a lot of tips and tricks these people used to improve performance.
My favorite story that I heard back in the day that influenced my perception on web performance is the incredible story of [Youtube Feather](https://blog.chriszacharias.com/page-weight-matters). In 2011, a developer decided to create a light weight version of Youtube. He released it only to find that page load time had increased. Only after geographically sorting through the data collected, did he find that, he had opened up Youtube to new markets where the internet was so slow that they couldn't even see videos in the earlier standard version. With the lighter version, they had to wait for 2 mins but they were able to actually access the content. This was super impactful to my psyche. The fact that having faster websites can change even one person's life drastically is something to aspire to as a web developer.

#### Personal experience

This is where I want to talk about my personal experience with building well performing web applications. When I worked at Flipkart, we got a chance to build the main ecommerce website from scratch. With an exclusive focus on performance, we truly pushed the boundary, at that time, of what needs to be done to have a feature rich yet lightweight website. We served as an inspiration to a whole generation of web developers and watched as Flipkart rose to new heights in terms of sales and engagement.

I then joined Atlassian 4 years back when they had just started moving our flagship product JIRA from JSP architecture to ReactJs. Very soon we realised that things had got slower as a result. It was very frustrating to realise that moving to a modern solution could so easily slide in terms of performance. Over the course of 4 years, we listened to tons of customer issues, measured and solved many perf related issues, saw the design system focus on perf, refactored code to make it work faster, replaced key pieces like navigation, routing and caching with a perf mindset. We also developed internal tools, reports and our own metrics to do so.
During this process I realised that at the scale of 7 million page visits a day, edge cases are much more common. We'd have to think about all these spectrum of users and provide them with a good experience. The magnitude of performance work is massive.

### Is it so hard to make faster web apps?

Yes, I believe that once a web app moves out of its "greenfield" phase and gains traction among users, has 10s or 100s of developers contributing to it, it gets really hard to align everybody on performance goals and budgets.

Here are a few specific reasons why I think perf engineering is hard.

### Legacy code

Performance work typically happens after products are released or after critical mass has adopted it. Slowly, developers realise that performance problems exist in their app. At this point of time, a lot of code has already been written. A dev who is trying to improve performance rarely gets to start from scratch. They always have to read existing code, make sure the test bed is solid and then perform strategic refactoring to fix the performance problems. Reading others code, refactoring it while keeping production intact is a task for the brave.

#### Insufficient tooling

On paper, there exists a lot of tooling to measure performance. But, as we discussed earlier, real user performance depends on various constantly changing factors. Most of our tooling test performance in synthetic(ideal) conditions whereas our users are probably using(abusing) the product features in unimaginable ways.
Real user testing can only happen after we make the code change and deploy it to production. This is a very slow feedback loop for developers. We cannot be sure that the metrics we measure during development will hold for our users using our app in the real world. This can be quite demotivating. While many companies have strategies to solidify their synthetic testing, we still get surprises from time to time after deployment. There can be no tools to get us real user data before the real users have used our code.

#### Measurement discipline

It is pretty tricky to know which things to measure in our app. It's only after some experience that we realise which changes affect which measurement. Many times a single metric does not give us the full picture. We may need multiple metrics to keep us on track. Some metrics may turn out to have diminishing value and its hard to recognise when that starts happening.
For eg - In JIRA, our page load metrics would change based on user usage. More users used our product during mornings and so we'd see a spike in performance metrics too as more users experienced the slower paths. Holidays would cause huge fluctuations and we learned not to trust weekend data. It was very hard to explain all these quirks in our data to new comers to our team. The documentation and discipline to maintain those helped us a lot.

### Conclusion

In summary,

1. This workshop will deal with client side web performance which boils down to JS/CSS/HTML on different browsers, under different bandwidth conditions and on different CPU strengths.

2. Web performance is a really important part of user experience and it must be treated as such. All frontend devs must learn the tools and tricks of the trade so that performance focus becomes a normalised part of web development. A well performing site is a delight to use and reflects in user satisfaction as well as conversion numbers.

3. Right now, web performance is quite hard. This is because of late treatment of perf aspects in the codebase, lack of standardised measurement, requirement of discipline and hard to predict user behaviour/circumstances.

However, I can assure you that many people build well performing websites with lots of functionality every day. All the platforms and tools are being improved every day to get you to the pit of success with regards to speedy web apps. This workshop and newsletter will be one such resource that will teach you the basic priciples of client side performance. Lets goo!
