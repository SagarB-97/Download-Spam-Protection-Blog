---
layout: post
title:  "Listening vs Telling"
date:   2018-05-15 18:00:12 +0530
categories: blogpost
excerpt: Decision on whether our state machine should 'listen' for downloads or if we should explicitly 'tell' our state machine about download events
---

Today, me and Nihanth had a discussion on which is the best model to proceed with. The `push` vs the `pull` model. 

- In the `push` (listen) model, the state machine listens for downloads and triggers state changes whenever a new downlaod is triggered.
- In the `pull` (tell) model, the download flow explicitly has a call to the state machine's state changing functions.

The `listen` model is extensible and is more suitable in situations where a number of listeners have subscribed to some broadcast event. This use case does not really fit the Download spam protection for firefox project's requirements. 

So we decided, we'll proceed with the 'tell' model.