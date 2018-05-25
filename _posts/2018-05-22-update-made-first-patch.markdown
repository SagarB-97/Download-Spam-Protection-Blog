---
layout: post
title:  "Update : Patch created"
date:   2018-05-22 18:00:12 +0530
categories: blogpost
excerpt: Made my first patch related to the project
---

Created Bug : [Implement a module that counts the number of downloads associated with an origin](https://bugzilla.mozilla.org/show_bug.cgi?id=1463527)

After some weeks of exploration and messing around the codebase, made my first patch today. 

The patch 'blocks' the second download associated with an 'origin'. The 'origin' chosen initially is the `source` property of the `Download` object defined in `toolkit/components/downloads/DownloadCore.jsm`.

The property that determines the 'origin' of a download has not yet been finalised and must be subjected to discussion.