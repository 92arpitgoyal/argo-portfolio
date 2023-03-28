---
is_external: true
excerpt: "Lately, I was working with Angular-UI bootstrap and wanted to
  customize Datepicker (Bootstrap Angular-UI’ Datepicker) as this:"
is_featured: false
templateKey: blog-post
content_img_path: ""
thumb_img_path: https://res.cloudinary.com/arpit-goyal/image/upload/v1558450058/images/featured-images/angular-ui-bootstrap.jpg
title: Customizing Angular-UI Bootstrap’ Directives
date: 2017-01-04T15:04:10.000Z
featuredpost: false
featuredimage: /img/angular-ui-bootstrap.jpg
url: https://medium.com/a-curious-life/customizing-angular-ui-bootstrap-directives-c4461c12afa9
subtitle: ""
description: Angular UI Bootstrap’ Default Datepicker (Left) to Modified Datepicker (Right)
tags:
  - Blog
---
Lately, I was working with Angular-UI bootstrap and wanted to customize Datepicker ([Bootstrap Angular-UI’ Datepicker](http://angular-ui.github.io/bootstrap/#/datepicker)) as this:

![](https://res.cloudinary.com/arpit-goyal/image/upload/v1558450058/images/featured-images/angular-ui-bootstrap.jpg)

<figcaption>Angular UI Bootstrap’ Default Datepicker (Left) to Modified Datepicker (Right)</figcaption>

It was not very difficult but, also not the easiest thing to do. Now, if you‘re planning to customize Angularjs components, your friend have it all figured out for you :

1. Recently with **release 0.14.0** AngularJS-UI **prefixed** all components.

Here are the changes that needs to be done for each component if you are upgrading to v0.14.0 [Migration guide for prefixes](https://github.com/angular-ui/bootstrap/wiki/Migration-guide-for-prefixes)

If not, your code is fine without prefixes, but you still need to understand that the documentation at there website is for post-release v0.14.0, so whatever component you use, **ignore uib** prefixes.

That means if you’re using v0.14.0 or later, use this custom directive :

    <uib-datepicker ng-model="dt"></uib-datepicker>

else, this (I will be using this, over this post) :

    <datepicker ng-model="dt"></datepicker>

***

2\. **Structural Changes / UI changes:**

Angular UI (ui-bootstrap-tpls.js) uses cached templates to create components, all you have to do is, pass some parameter to custom directive be able to customize some features like min-date, max-date, format-day-header, etc.

But, if you want to do more, you need to edit templates of directives, [StackOverflow | Can you override specific templates in Angular-UI Bootstrap](http://stackoverflow.com/questions/17660947/can-you-override-specific-templates-in-angularui-bootstrap)?

So, as per rules of being lazy, I added these scripts to my **index.html,** it took care of restructuring of Previous and Next button on Day selection.

<script src="https://gist.github.com/92arpitgoyal/c84e99583d9b0e7ec207.js.js"></script>

and also added some styles to change colors and cell-spacing.

(and also I am using [materialdesignicons](https://materialdesignicons.com/) so I replaced defaults icons with same)

and It works yuhu!, There are other \[better\] ways of doing same, I could have fed it directly to **$templateCache,** but there’s even a better way to do it (**Best practices** rules), all I did was, moved these template’ markups to actual .html files and place them under a folder _custom-templates,_ then used provider **$provide.decorator** to override directive’s templateUrl to point at it. (hence avoiding the need to directly mess around with **$templateCache**)

![](https://res.cloudinary.com/arpit-goyal/image/upload/v1558072062/images/Customizing-Angular-UI-Bootstrap--Directives/1*LvxLvXZiDsH16gFpoP_dXg.png)

<figcaption>Custom template markup files</figcaption>

Then I wrote simple provider code to overwrite make custom templates be used for directives.

I just added following code to **app.js** file.

<script src="https://gist.github.com/92arpitgoyal/e934c15289402e8f7e8d.js.js"></script>

and it was working fine having most of the changes I wanted.

If you’re looking for no structural change but only changes in Styles, you should make use of \`custom-class\` _(date, mode) (Default: null) : An optional expression to add classes based on passing date and current mode (day|month|year),_ to add classes and then modify Styles using those styles.

***

3\. **Format-day-header**

Designs had **_day in week header_** with one letter **(‘S’ for Sunday, ‘M’ for Monday and so on.)**

![](https://res.cloudinary.com/arpit-goyal/image/upload/v1558072062/images/Customizing-Angular-UI-Bootstrap--Directives/1*fUnWgi5X8rq3-H68RUf5qg.png)

So I added **format-day-header=”E”** as an attribute  to my datepicker, and it still gave me the default headers i.e: sun, mon, tue, etc.

Then I tried **format-day-header=”EEEE”, and it worked fine, gave ‘**_sunday’, ‘monday’, etc._

Clearly **“E”** was not an acceptable value, I thought it’s an obvious choice to have just initials of very obvious things (‘S’ for Sunday, ‘M’ for Monday, etc.). This should have an easy solution sitting there, I googled a bit and found nothing but more questions,  
[angular-ui-datepicker-format-day-header-format-with-with-2-letters](http://stackoverflow.com/questions/31942788/angular-ui-datepicker-format-day-header-format-with-with-2-letters)

Then I dug deep and searched for **“EEE”** in **ui-bootstrap-tpls.js** and found it using **regex: $locale.DATETIME_FORMATS.SHORTDAY.join(‘|’)** for feeding the value for EEE attributes, if you do same, you will find that there is nothing for **E** and hence the easy and lazy way will be to search for **SHORTDAY** in **angular.js** and **edit** the value for **SHORTDAY** to required values i.e overriding.

    "SHORTDAY": [  
      "S",  
      "M",  
      "T",  
      "W",  
      "T",  
      "F",  
      "S"  
    ],

But yes, the rule book of best practices says **don’t edit vendor files,** so created one more decorate provider :

<script src="https://gist.github.com/92arpitgoyal/10b9e0a3a110f7ddaba7.js.js"></script>

You should add it to old config itself, if you’re planning to use both.

![](https://res.cloudinary.com/arpit-goyal/image/upload/v1558072062/images/Customizing-Angular-UI-Bootstrap--Directives/1*CtssURT2ZoiaD3HE9D4XaA.png)

After this final step, and adding some styles, I also passed some already available parameters like **format-day=”d”, show-weeks=”false”.**

Finally, I got this beautiful looking datepicker, without using any additional plugin, just customizing the default directive given by Angular-UI Bootstrap, which happens to retain all keyboard control support users would need.

***

So a template was changed to something very different, using Best practices and least possible code, now you can go ahead and create your own custom template for Directives already there.

<iframe src="https://contentupgrade.me/eW4az9JY.html?ref=" width="700" height="350" frameborder="0" scrolling="no"></iframe>

If you have any questions / suggestions, feel free to drop it in comments and If you found this article useful, click the heart below.

![](https://res.cloudinary.com/arpit-goyal/image/upload/v1558072062/images/Customizing-Angular-UI-Bootstrap--Directives/1*emxDrmtEiMy6h5JXecj-Og.gif)