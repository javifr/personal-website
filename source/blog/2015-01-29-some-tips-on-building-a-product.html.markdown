---
title: Some tips on building a product
date: 2015-01-29 12:32 UTC
tags: order, notes, product, tips
published: false
---

# Some tips on building a product

As I've learned interesting things with the whole expercience of building [Overstand](http://www.overstand.io) in no more than 4-5 days, and came across very good resources, i'll share a quick list that can help you if you are trying to build a prototype, a product or a proof of concept.

Keep in mind that my focus was to build quick, to build something useful that would be available in as many devices as possible ( smartphones, tablets, desktop, whatever... ), spending no money in infrastucture and above all to end with a real appealing product.

## Idea
- Ideas pool: Check [Hacker news idea sunday](https://news.ycombinator.com/item?id=7616910)
- New tech products, frameworks or technologies: It always [excites my mind](http://lifesphilosophie.com/wp-content/uploads/2014/09/rmf4c.gif)

## Naming
- Avoid strange/ugly/long/compound/triple-letters names and domains. Today there's a lot of choice in domain extensions ( .io, .guru, etc...). A one word understandable name makes everything easy.
- If it's possible, try to be **meaningfull**. If your name has a significant background It'll be better, it'll help you to explain your product, and it'll help your users to remind the name of your product.

## Branding / Design / Hell
If you don't have great graphic design skills, as i don't, but you have a trained eye, for sure you'll now about the frustration of trying to design something nice and coherent in photoshop. I've found this resources very helpfull in order to maximize my effort in this field.

- [Google Ventures - Design Library](https://www.gv.com/library/design)
- [Adobe kuler](https://color.adobe.com/): Great for choosing a color scheme
- [App icon template](http://appicontemplate.com/): Great for icon designing, and store screenshots of your app. It comes with cool gradient color schemes!
- [Dribbble](https://dribbble.com/): A great place to learn, find inspiration, color schemes, ideas, etc...

## Development: 
- Angularjs
- Cordova
- iOnic framework
- Divshot
- Bower
- Gulp

## Publish to stores 

I've build my product with [Ionic Framework](http://ionicframework.com/), so the next tips will be conditioned to this. Even though, you'll find a lot of common ground if you are building with another framework.

### Android Store

First, take a look at [this](http://ionicframework.com/docs/guide/publishing.html) resource.

Some tips on top of the previous link:

- ```ionic resources```( [Generate your icons and splashscreens](http://ionicframework.com/blog/automating-icons-and-splash-screens/) )
- Change [config.xml](http://cordova.apache.org/docs/en/4.0.0/config_ref_index.md.html) vars ( if you don't change **android-versionCode** in every release you won't be able to publish ).

### iTunes store

Once you've got your whole certificate, provisioning, and evil stuff you can go this way:

- ```ionic resources```( [Generate your icons and splashscreens](http://ionicframework.com/blog/automating-icons-and-splash-screens/) )
- ```ionic build ios```
- ```ionic run ios```( try it on your device )
- Open Xcode file found in ```/platforms/ios/```
- Make sure a **device** is selected, and not a simulator
- Product > Scheme > edit scheme ( select archive and close )
- Product > archive
- Validate
- Submit
- Then go to [iTunes Connect](https://itunesconnect.apple.com) and don't forget to select the version you wanna publish ( Is in the "Compilation" section , see a plus sign? not easy.. ) 


## Marketing website
- You'll need **pictures** in order to have a nice marketing material, you could end up in [ShutterStock](http://www.shutterstock.com/) but it'll cost you money and the "coolness" of the pictures is not that high. So, welcome [stock photos that don't suck](https://medium.com/@dustin/stock-photos-that-dont-suck-62ae4bcbe01b). And don't forget about pictures of your product with some context, take a look at [PlaceIt](https://placeit.net/).
- If you dont' want to code from the ground your marketing website, take a look at [Theme Forest](http://themeforest.net/search?utf8=%E2%9C%93&term=bootstrap), and filter by [bootstrap](http://themeforest.net/search?utf8=%E2%9C%93&term=bootstrap). Choosing [twitter bootstrap](http://getbootstrap.com/) is always a good choice if you want to build fast and reliable.
- Use a **static html generator** instead of only a bunch of html files. [Middleman](https://middlemanapp.com/) is the best I've found. It has blogging capabilities, native multilanguage support and it's very easy to use.
- In order to **host your site**, why spending money on a server if you could use [[Divshot](https://divshot.com/) or [Github Pages](https://pages.github.com/)?


## Marketing: 

- Nice read in order to excite your marketing ideas: [Trust Me, I'm lying: Confessions of a Media Manipulator](http://www.amazon.com/Trust-Me-Lying-Confessions-Manipulator/dp/1591846285)
- [Google Ventures - Marketing Library](https://www.gv.com/library/marketing)