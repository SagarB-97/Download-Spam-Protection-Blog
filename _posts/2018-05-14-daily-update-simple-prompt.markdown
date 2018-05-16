---
layout: post
title:  "Daily Update : Messing around. User prompt displayed for second download"
date:   2018-05-14 18:00:12 +0530
categories: blogpost
excerpt: There was an attempt - To display user prompt before the second download of the session
---

The *Google summer of code* **coding period** officially begins today.

Nihanth (@nhnt11) suggested that I try and experiment with the code base to make an attempt at dumping an output to the terminal whenver a download is initiated. This would give us an idea about possible 'plugpoints' in the code base where we can plug in our logic.

I realised `toolkits/components/downloads/DownloadCore.jsm` is the javascript module where a download is actually initiated. Some alteration was made in `DownlaodCore.jsm` and `DownlaodUIHelper.jsm` to try and display a user prompt asking if the user would like to preoceed with the second download of the session.

However, because of failure in synchronisation, the download was executed before the prompt was shown. Anyway, we got an idea of possible *plugpoints* to plug in our logic.

See diff below. It might not follow conventions, standards and practices and it's not meant to be a complete patch or a commit.

``` diff
diff --git a/toolkit/components/downloads/DownloadCore.jsm b/toolkit/components/downloads/DownloadCore.jsm
--- a/toolkit/components/downloads/DownloadCore.jsm
+++ b/toolkit/components/downloads/DownloadCore.jsm
@@ -35,6 +35,8 @@ ChromeUtils.defineModuleGetter(this, "Se
                                "resource://gre/modules/Services.jsm");
 ChromeUtils.defineModuleGetter(this, "PrivateBrowsingUtils",
                                "resource://gre/modules/PrivateBrowsingUtils.jsm");
+ChromeUtils.defineModuleGetter(this, "DownloadUIHelper",
+"resource://gre/modules/DownloadUIHelper.jsm");
 
 XPCOMUtils.defineLazyServiceGetter(this, "gDownloadHistory",
            "@mozilla.org/browser/download-history;1",
@@ -57,6 +59,8 @@ const BackgroundFileSaverStreamListener
       "@mozilla.org/network/background-file-saver;1?mode=streamlistener",
       "nsIBackgroundFileSaver");
 
+var count = 0;
+
 /**
  * Returns true if the given value is a primitive string or a String object.
  */
@@ -308,6 +312,7 @@ this.Download.prototype = {
    * @rejects JavaScript exception if the download failed.
    */
   start: function D_start() {
+   
     // If the download succeeded, it's the final state, we have nothing to do.
     if (this.succeeded) {
       return Promise.resolve();
@@ -416,6 +421,7 @@ this.Download.prototype = {
                                     becauseTargetFailed: true });
         }
 
+
         // Disallow download if parental controls service restricts it.
         if (await DownloadIntegration.shouldBlockForParentalControls(this)) {
           throw new DownloadError({ becauseBlockedByParentalControls: true });
@@ -427,6 +433,24 @@ this.Download.prototype = {
           throw new DownloadError({ becauseBlockedByRuntimePermissions: true });
         }
 
+        /******************************* */
+        count = count + 1;
+        dump("Download triggered\n");
+        if(count>1){
+          dump("Ask user\n");
+          if(!(await DownloadUIHelper.getPrompter().confirmMultipleDownloads())){
+            dump("Blocked by user\n");
+            throw new DownloadError({ becauseBlockedByRuntimePermissions: true });         
+          }
+          else {
+            dump("Allowed by user\n");
+          }
+        }
+        else {
+          dump("Allow download\n");
+        }
+        /******************************** */
+
         // We should check if we have been canceled in the meantime, after all
         // the previous asynchronous operations have been executed and just
         // before we call the "execute" method of the saver.
@@ -436,6 +460,7 @@ this.Download.prototype = {
         }
 
         // Execute the actual download through the saver object.
+
         this._saverExecuting = true;
         try {
           await this.saver.execute(DS_setProgressBytes.bind(this),
diff --git a/toolkit/components/downloads/DownloadUIHelper.jsm b/toolkit/components/downloads/DownloadUIHelper.jsm
--- a/toolkit/components/downloads/DownloadUIHelper.jsm
+++ b/toolkit/components/downloads/DownloadUIHelper.jsm
@@ -154,6 +154,23 @@ this.DownloadPrompter.prototype = {
     }
   },
 
+  confirmMultipleDownloads() {
+    try {
+ 
+      let s = DownloadUIHelper.strings;
+   
+      let checkState = { value: false };
+      let shouldLaunch = this._prompter.confirmCheck(
+                           "Multiple downloads",
+                           "Are you sure you want to allow multiple downloads?",
+                           s.fileExecutableSecurityWarningDontAsk,
+                           checkState);
+      return Promise.resolve(shouldLaunch);
+    } catch (ex) {
+      return Promise.reject(ex);
+    }
+  },
+
   /**
    * Displays a warning message box that informs that there are active
    * downloads, and asks whether the user wants to cancel them or not.
```
