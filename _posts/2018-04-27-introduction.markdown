---
layout: post
title:  "Introduction"
date:   2018-04-27 11:24:12 +0530
categories: blogpost
excerpt: A general introduction about the project
---

Mozilla is one of the biggest open source organisations that has been participating in Google Summer of Code since 2005. This blog is associated with one of Mozilla's Google Summer of Code projets in 2018 entitiled `Download Spam Protection in Firefox`.

- Student : **Sagar Bharadwaj** (:sagarb_97)
- Mentor : **Nihanth Subramanya** (:nhnt11)
- Reporter : **Johann** (:johannh)
- Realted Meta Bug : [Bug 1306334](https://bugzilla.mozilla.org/show_bug.cgi?id=1306334) on Bugzilla

The project description in brief (directly borrowed from [Mozilla's ideas page](https://wiki.mozilla.org/Community:SummerOfCode18)) is as follows :

> In Firefox, we want websites to go through a permission prompt if they want to initiate several downloads in an automated (not user-initiated) fashion. The project will involve implementing a permission prompt, implementing the logic of when it should be triggered, and collecting data (via telemetry, etc) on how users interact with the prompt in order to verify how the feature behaves.

### Why is Download spam protection needed?

The internet is filled with malicious websites that attempt to bombard the user with multiple downloads and end up hogging the user's bandwidth. Browsers are generally *gateways* to the big, bad internet. Browsers thus have an inherent responsibility to protect its users from such websites with malicious intent.

Mozilla Firefox currently has no feature that shields users from spam downloads. Implementing this feature is not a trivial task. Some reasons supporting this claim :

- A mechanism to differentiate between user initiated and automatically initiated downloads has to be established as it is undesirable to annoy the user with unnecessary warning and permission prompts even when the user himself has requested for a download.

- A data collection technique (via telemetry) has to be implemented to measure the effectiveness of the implemented logic.

### Basic Implementation scheme

The project proposal contained two possible approaches to the problem statement. One of these approaches involves implementation of a state machine and was selected as the feasible solution to the problem.

*Alogorithm* :
```
Maintain a state machine for every open page.

- Initially the state is 'ALLOW_COUNT_DOWNLOADS'.
- After 'count' number of downloads are triggered, the state is transitioned to 'PROMPT_BEFORE_DOWNLOAD'.
  The value of count is to be determined. In chromium browser, the count is set to 1.
- Whenever a page in 'PROMPT_BEFORE_DOWNLOAD' state triggers a download, the user is shown a permission prompt.
  It is confirmed if the user wants to permit multiple downloads.
- The page's state is set to 'ALLOW_DOWNLOADS' whenever currentDocument -> UserHasInteracted() transitions to true.
  This state allows all downloads as the user is now interacting with the page and is probably aware of all the downloads.
```

Project implementation details will be decided as and when the project progresses. For example, this approach seems like it has a severe flaw. User's interaction is equivalent to granting permission. However, this will soon be worked out.

The detailed implementation scheme will be described in a blog post made at the end of the project

### Project Deliverables

- A mechanism to differentiate between user initiated and automatic downloads
- A mecahnism to *protect* the user from automatic downloads
- Verification of the above mechanism by implementing data collection techniques (via Telemetry)
- User interface for permission prompts asking if the user trusts a particular website initiating multiple downloads

### Links

Project abstract : [GSoC Website](https://summerofcode.withgoogle.com/projects/#4877582533656576)

Meta bug : [Bug 1306334](https://bugzilla.mozilla.org/show_bug.cgi?id=1306334)

Related Bugs : [Dependency Tree](https://bugzilla.mozilla.org/showdependencytree.cgi?id=1306334&hide_resolved=1)

Test Web Application : [Github Repo](https://github.com/SagarB-97/Download-Spam-Protection-in-Firefox)

Blog Code : [Github Repo](https://github.com/SagarB-97/Download-Spam-Protection-in-Firefox/tree/gh-pages)

