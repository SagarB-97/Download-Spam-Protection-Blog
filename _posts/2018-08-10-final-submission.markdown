---
layout: post
title:  "A Summary my work"
date:   2018-08-10 18:00:12 +0530
categories: blogpost
excerpt: A summary of my work during Google Summer of Code. Also, what's left!
permalink: /gsoc-summary/
---

This is has been, by far, my most productive summer. Had lots of fun working with the Mozilla community. Piqued myself with intriguing details of browser engineering. [Mozilla Blog](https://blog.mozilla.org/) and [Mozilla Hacks](https://hacks.mozilla.org/) never failed to keep me entertained :D

Now it's all coming to an end. Not my efforts to stay engaged with the Mozilla community, but Google summer of code. So this post is a summary of what I've been upto in the last couple of months. 

1. These months have been an immense learning experience. I learnt how difficult it is to complete even the most trivial tasks when the code base is huge. Adhering to software engineering principles is necessary and fun but also painful. 

2. Played around with different components in Firefox code base : C++, Javascript, XPCOM. Learnt how all of them maintian cohesion and how they interact with each other. Wrote an XPCOM inerface for the project.

3. Produced usable code. Implemented the basic Download spam protection mechanism. Spent time studying the code base and finding suitable plugpoints to insert the spam protection mechanism.

### What has been done?

0. A web application that can serve as a test case for initiating multiple automatic and user inititated downloads.
1. XPCOM interface for the backend to invoke the front end download count mecahnism.
2. Download count mechanism in the front end to count the number of downloads associated with its origin
3. Associate a download with its invoking browser element and display a prompt in that browser element.
4. Mechanism to queue up downloads triggered while the prompt is being shown so that they can be allowed or blocked in the *future* once the user takes an action

Test Web Application : [GitHub Repo](https://github.com/SagarB-97/Download-Spam-Protection-in-Firefox)

The bug that I worked on : [Bug 1463527](https://bugzilla.mozilla.org/show_bug.cgi?id=1463527)

I've 'documented' some of my thought process as comments on this bug. Although most of it happened over IRC and private IRC messages with Nihanth. 

Mozilla has begun using [Phabricator](https://phabricator.services.mozilla.com/) for code reviews recently. I submitted all my patches to Phabricator in an incremental fashion.

Links to my pathces : 

- [https://phabricator.services.mozilla.com/D2434](https://phabricator.services.mozilla.com/D2434)
- [https://phabricator.services.mozilla.com/D2452](https://phabricator.services.mozilla.com/D2452)
- [https://phabricator.services.mozilla.com/D2453](https://phabricator.services.mozilla.com/D2453)
- [https://phabricator.services.mozilla.com/D2458](https://phabricator.services.mozilla.com/D2458)
- [https://phabricator.services.mozilla.com/D2464](https://phabricator.services.mozilla.com/D2464)
- [https://phabricator.services.mozilla.com/D2466](https://phabricator.services.mozilla.com/D2466)

Mozilla has a try server to run tests and make sure the new code is not meddling with previous work flows. The test results are reported using [Treeherder](https://treeherder.mozilla.org/). 

Treeherder results for my patch : 

[https://treeherder.mozilla.org/#/jobs?repo=try&revision=e08c28f74021782a2726327e3faf35cf11f44b20](https://treeherder.mozilla.org/#/jobs?repo=try&revision=e08c28f74021782a2726327e3faf35cf11f44b20)

(*Note* : Failures are intermittent and unrelated to my patch)

### What's left?

- UI/UX tweaks and tests
- Localisation of Permission prompt
- Differentiating between user initiated and automatic downloads

Currently, the download spam protection feature is hidden behind a *pref*. After elaborate testing and reviews, the pref will be flipped by default and shipped with nightly. 




