---
layout: post
title:  "The long forgotten download flow"
date:   2018-06-08 18:00:12 +0530
categories: blogpost
excerpt: Documented a part of the download flow in Firefox. It's huge.
---

Ability to download is one of the most archaic features of any browser, intoduced during web's infancy. Most of the download related core code lies untouched and rusty for a couple of decades. Yes, *decades*. This is surprising given how *mercurial* Firefox code base is :p

In order to proceed, I spent sufficient time reading through and documenting the download flow.

### How?

[Searchfox](https://searchfox.org/), the real MVP. 

> Searchfox is a source code indexing tool for Mozilla Firefox. It indexes C++, Rust, and JavaScript code.

It indexes idl, xul, xbl as well. It includes cross-referencing and path filtering features also.

Gone are the `ctrl + shift + f` days; Yeah, that's how I searched through the code base, back when I was dense and unaware of *source code indexers*. Thanks Nihanth :)

Searchfox is the best at what it does (imo). By the way, there are other places you can search through the Firefox code base like [dxr](https://dxr.mozilla.org/mozilla-central/source/) and  [hg.mozilla.org](https://hg.mozilla.org/). Searchfox however is recommended : [Bill McCloskey's compariosion Blog](https://billmccloskey.wordpress.com/2016/06/07/searchfox/).

### The flow

Here is the the download flow I *discovered* by digging through the 'long forgotten' code base. I started from `uriloader/exthandler/nsExternalHelperAppService.cpp` and *traced up and down* the function call stack. Here's what I found : 

(It starts at `nsIURILoaded.cpp` because I was convinced tracing back further is simply not necessary for the problem statement at hand)

I have given Searchfox permalinks to all the code points

1. Starts in `nsURILoader.cpp`, `nsDocumentOpenInfo::OnStartRequest` : [here](https://searchfox.org/mozilla-central/rev/db79375c66cdd69848223e9dbff5e9d2490ec3b2/uriloader/base/nsURILoader.cpp#212)

2. Through : [this function call](https://searchfox.org/mozilla-central/rev/db79375c66cdd69848223e9dbff5e9d2490ec3b2/uriloader/base/nsURILoader.cpp#306) to [nsDocumentOpenInfo::DispatchContent](https://searchfox.org/mozilla-central/rev/db79375c66cdd69848223e9dbff5e9d2490ec3b2/uriloader/base/nsURILoader.cpp#378)

3. Checking if `Content-Disposiotion` is set to `attachment` : [here](https://searchfox.org/mozilla-central/rev/db79375c66cdd69848223e9dbff5e9d2490ec3b2/uriloader/base/nsURILoader.cpp#416).<br>
**I think we should plug in our permissions checking code somewhere here. The temp file has not been created here and the download hasn't started yet. So this means download spam does not hog user's bandwidth.**

4. Through : [this function call](https://searchfox.org/mozilla-central/rev/db79375c66cdd69848223e9dbff5e9d2490ec3b2/uriloader/base/nsURILoader.cpp#611) to [nsExternalHelperAppService::DoContent](https://searchfox.org/mozilla-central/rev/704612cf4426f0f0510b3e160895578c319a3270/uriloader/exthandler/nsExternalHelperAppService.cpp#697)

5. Through : [this statement](https://searchfox.org/mozilla-central/rev/704612cf4426f0f0510b3e160895578c319a3270/uriloader/exthandler/nsExternalHelperAppService.cpp#705) to [nsExternalHelperAppService::DoContentContentProcessHelper](https://searchfox.org/mozilla-central/rev/704612cf4426f0f0510b3e160895578c319a3270/uriloader/exthandler/nsExternalHelperAppService.cpp#619)

6. Through : [this call](https://searchfox.org/mozilla-central/rev/704612cf4426f0f0510b3e160895578c319a3270/uriloader/exthandler/nsExternalHelperAppService.cpp#693) to [ExternalHelperAppChild::SetHandler](https://searchfox.org/mozilla-central/rev/db79375c66cdd69848223e9dbff5e9d2490ec3b2/uriloader/exthandler/ExternalHelperAppChild.h#33)

7. And then [ExternalHelperAppChild::OnStartRequest](https://searchfox.org/mozilla-central/rev/db79375c66cdd69848223e9dbff5e9d2490ec3b2/uriloader/exthandler/ExternalHelperAppChild.cpp#77)

8. Through : [this call](https://searchfox.org/mozilla-central/rev/db79375c66cdd69848223e9dbff5e9d2490ec3b2/uriloader/exthandler/ExternalHelperAppChild.cpp#79) to 
[nsExternalAppHandler::OnStartRequest](https://searchfox.org/mozilla-central/rev/704612cf4426f0f0510b3e160895578c319a3270/uriloader/exthandler/nsExternalHelperAppService.cpp#1564)

9. And then `nsExternalHelperAppService::DoContent()` and then back to `nsExternalHelperAppService::OnStartRequest()`

10. Decision on whether or not to invoke the dialog starts [here](https://searchfox.org/mozilla-central/rev/704612cf4426f0f0510b3e160895578c319a3270/uriloader/exthandler/nsExternalHelperAppService.cpp#1683).
<br>**By now, the temp file has been set up and transfer has begun in the background. We can also put permissions checking code here and cancel the download based on results. This deletes the temp file and stops the transfer.**
<br>If dialog need not be invoked, `SaveToDisk` is directly called : [here](https://searchfox.org/mozilla-central/rev/704612cf4426f0f0510b3e160895578c319a3270/uriloader/exthandler/nsExternalHelperAppService.cpp#1781)

11. Dialog invoked [here](https://searchfox.org/mozilla-central/rev/704612cf4426f0f0510b3e160895578c319a3270/uriloader/exthandler/nsExternalHelperAppService.cpp#1741)

12. Dialog `show` function is [here](https://searchfox.org/mozilla-central/rev/db79375c66cdd69848223e9dbff5e9d2490ec3b2/toolkit/mozapps/downloads/nsHelperAppDlg.js#132) : `nsUnknownContentTypeDialog.show()`
<br>**We should put permissions checking code here if we should always respect user's preferences for a particular mime type irrespective of how many downloads are triggered**

13. Through : [this](https://searchfox.org/mozilla-central/rev/db79375c66cdd69848223e9dbff5e9d2490ec3b2/toolkit/mozapps/downloads/nsHelperAppDlg.js#148), a timer is started

14. `notify` function [here](https://searchfox.org/mozilla-central/rev/db79375c66cdd69848223e9dbff5e9d2490ec3b2/toolkit/mozapps/downloads/nsHelperAppDlg.js#534)

15. Through : [this call](https://searchfox.org/mozilla-central/rev/db79375c66cdd69848223e9dbff5e9d2490ec3b2/toolkit/mozapps/downloads/nsHelperAppDlg.js#537) to [reallyShow() function](https://searchfox.org/mozilla-central/rev/db79375c66cdd69848223e9dbff5e9d2490ec3b2/toolkit/mozapps/downloads/nsHelperAppDlg.js#155)

16. After dialog, [SaveToDisk function](https://searchfox.org/mozilla-central/source/toolkit/mozapps/downloads/nsHelperAppDlg.js#544) is invoked  (Temp file to target file)

17. [SaveToDisk function](https://searchfox.org/mozilla-central/rev/db79375c66cdd69848223e9dbff5e9d2490ec3b2/toolkit/mozapps/downloads/nsHelperAppDlg.js#544)

18. Through : [this call](https://searchfox.org/mozilla-central/rev/704612cf4426f0f0510b3e160895578c319a3270/uriloader/exthandler/nsExternalHelperAppService.cpp#2298) to [ nsExternalAppHandler::ContinueSave](https://searchfox.org/mozilla-central/rev/704612cf4426f0f0510b3e160895578c319a3270/uriloader/exthandler/nsExternalHelperAppService.cpp#2303)

19. `CreateTransfer` invoked (where actual transfer starts) : [here](https://searchfox.org/mozilla-central/rev/704612cf4426f0f0510b3e160895578c319a3270/uriloader/exthandler/nsExternalHelperAppService.cpp#2344)

20. [Create Transfer function](https://searchfox.org/mozilla-central/rev/704612cf4426f0f0510b3e160895578c319a3270/uriloader/exthandler/nsExternalHelperAppService.cpp#2126)
Through : [this statement](https://searchfox.org/mozilla-central/rev/704612cf4426f0f0510b3e160895578c319a3270/uriloader/exthandler/nsExternalHelperAppService.cpp#2157), the flow finally enters the download module.

After this [download.start()](https://searchfox.org/mozilla-central/rev/db79375c66cdd69848223e9dbff5e9d2490ec3b2/toolkit/components/downloads/DownloadCore.jsm#310) is eventually called.

**My proposal suggested that I plug in permission checking code somewhere here**. However, it appears I was so wrong and some major proposal refactoring is required.