---
layout: post
title:  "Google Chrome's behaviour on multiple downloads"
date:   2018-05-02 11:24:12 +0530
categories: blogpost
excerpt: Chrome's reaction to multiple downloads when it was connected to the test web application's node server
---

This post serves as a reference to Mozilla Firefox's expected behaviour at the end of the project when subjected to multiple downloads. Google Chrome already has the Download spam protection feature in place.

Chrome was connected to the Test Web application's Nodejs server and the following observations were made :

- Chrome does not show a prompt irrespective of how many times downloads are initiated by clicking on the `Initiate Single Download` button.

- It shows the prompt when the second download is initiated by the `Initiate 5 downloads` button. This happens even if multiple files have been downloaded using the single download button earlier.

- It shows prompt for the second download initiated in the `delay page`. (delay page is opened when `Initiate downloads after delay` is clicked. The downloads are initiated every 10s seconds in this page without any user activity.)