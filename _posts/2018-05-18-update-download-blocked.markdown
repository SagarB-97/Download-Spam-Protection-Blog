---
layout: post
title:  "Update : Download Blocked"
date:   2018-05-18 18:00:12 +0530
categories: blogpost
excerpt: Plugging into the flow and attempting to block a download.
---

Today, I made an attempt at blocking the download altogether and writing a landable commit. However, I could not really figure out where the 'Save file' user dialog is invoked.

So the dialog was displayed. But irrespective of what the user chooses (`Save file`, `Open` or `Cancel`), the download was 'blocked'. That is, the file wasn't downloaded. However, we have to explore new 'plug points` to plug in state changing code and stop the dialog from getting invoked.

As of now, the focus is not really on 'blocking' the download. It's more on identifying the 'source of truth' to map the download to its origin.