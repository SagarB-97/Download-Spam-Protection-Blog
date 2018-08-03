---
layout: post
title:  "Javascript : Prototypal inheritance"
date:   2018-06-11 18:00:12 +0530
categories: blogpost
excerpt: Inheritance in javascript. Something that always confused me.
---

Javascript has a gargantuan number of features that are popularly considered incomprehensible by programmers coming from *traditional* languages. While `Promises`, javascript's asynchrony (inspite of being single threaded), variable scope are the *big guns* when it comes to obsurity, prototypal inheritance isn't really in the lead.

However, it was always a boggling concept for me (and still is :p) and hence the post. 


### Prototypal inheritance

*There are no classes in javascript*. Yes, it has the `class` keyword starting from ECMAScript 2015, but that's just *syntactical sugar*. I say there are no *classes*, referring to how *classes* are defined and used in typical *Class-based* inheritance models. 

In *prototypal inheritance*, there are only *prototypes*. *Prototypes* are just javascript objects. So, that's it. In javascript, *objects inherit from other objects*. (Compare that to *classes inherit from other class and objects implement classes*).

Why is this a big thing? What are the advantages over classical inheritance? Is it worth all the mess and perplexity? The internet is filled with questions from confused souls like me and answers from enlighetend souls alike.

Prototypal inheritance is whaat makes *compositional* inheritance possible. Compositional inheritance is when you build your *types* around *what they do*. In classical inheritance, you build your *types* around *what they are*.

Firefox javascript code base is filled with javascipt objects declared using constructors and `prototype` keyword. As in :

```javascript
function constructor() {
  this.property = value;
}

constructor.prototype = {
  // properties
}

var obj = new constructor();
```

>Prototypes are the mechanism by which JavaScript objects inherit features from one another.

The prototype keyword is described in [this](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Object_prototypes) MDN article

