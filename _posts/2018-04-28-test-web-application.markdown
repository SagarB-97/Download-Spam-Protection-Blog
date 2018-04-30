---
layout: post
title:  "Test Web Application"
date:   2018-04-28 11:24:12 +0530
categories: blogpost
excerpt: A post about the Web application built as a test case for the project
---

Acquiring test cases for this project is not a trivial task. One will have to scour the internet to find websites that initiate multiple downloads.

Doing this will not only result in an unreliable test case, but would also mean that testing the feature would require an internet connection. To avoid such hassles, it was decided that we'll go about developing a web application by ourselves that would imitate malicious websites trying to initiate several downloads. This application can then be hosted locally and test cases can be written to connect to this service without the need for any internet connection.

The Test web application code is hosted [here](https://github.com/SagarB-97/Download-Spam-Protection-in-Firefox)

(*Note* : The `gh-pages` branch of the same GitHub repository contains this blog's code)

### What the application does?
*Note* : The test case will be developed as and when new requirements are identified.

The application has three buttons currently :
- Initiate Single download
- Initiate multiple downlaods
- Initiate multiple downloads with a delay

(Functionalities are self describing)

The file downloaded in each case is a *184 Bytes* text file.

### How to launch the application?

`node (version : 8.9.3)`

Make sure node is installed. Installing node will also install `npm` - *Node Package Manager*.

**Clone this repository**
```
git clone https://github.com/SagarB-97/Download-Spam-Protection-in-Firefox.git

cd Download-Spam-Protection-in-Firefox
```

**Navigate to the folder** :
```
cd Web-Application-test-case/Server/
```

**Install all required dependencies** :
```
npm install
```
This will automatically fetch all required dependencies by referring to `package.json` file

**Start the server** :
```
npm start
```

### Application Screenshot

![screenshot]({{ "/assets/testapp_screenshot.png" | absolute_url}})
