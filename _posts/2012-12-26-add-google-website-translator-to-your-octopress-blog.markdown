---
layout: post
title: "Add Google Website Translator to your Octopress Blog"
date: 2012-12-26 20:06
comments: true
author: Jatin Ganhotra (@jatinganhotra)
categories: [Google Website Translator, Octopress, Blogging-tips, Blog]
sharing: true
footer: true
---

<link href='http://fonts.googleapis.com/css?family=Coming+Soon&subset=latin,latin-ext' rel='stylesheet' type='text/css'>

<div>
<span style="float:right;" id="google_translate_element"></span>
<span style="float:right; font-family: 'Coming Soon', cursive;">Having trouble viewing in English, Choose Your Language : &nbsp;&nbsp;&nbsp;</span>
</div>
<BR>&nbsp;<BR>

Most countries in the world have English as their [official language](http://en.wikipedia.org/wiki/List_of_countries_where_English_is_an_official_language), but there are many countries where people [don't know English](http://en.wikipedia.org/wiki/File:English-as-Official-Language_Map.png). So, it's good to have a website translator if people from such countries visit your blog. This article will show you how to add [Google Website Translator](http://translate.google.com/manager/website/) to your [Octopress](http://octopress.org/) blog.  

Recently, [Google](http://www.google.com/) [announced in a post](http://googletranslate.blogspot.in/2012/05/now-you-can-polish-up-googles.html) on their [Google translate blog](http://googletranslate.blogspot.in/) that they are introducing a beta website translator for blogs and websites.  Google translate currently works over 60 languages.  Once you add the customization meta tag to a webpage, visitors will see your customized translations whenever they translate the page, even when they use the feature in [Chrome](https://www.google.com/intl/en-GB/chrome/browser/features.html) and [Google Toolbar](http://www.google.com/toolbar/ie/index.html). They’ll also now be able to '[suggest a better translation](http://googleblog.blogspot.in/2007/03/suggest-better-translation.html)' when they notice a translation that’s not quite right, and later you can accept and use that suggestion on your site.  

For more information about better translation and suggestion, refer [here](http://translate.google.com/manager/website/). You can follow these easy steps to add Google Website Translator to your Octopress blog:  

#### Step 1 : Get `<meta>` tag and `<script>`'s which the Website Translator plugin uses  

1.  Go to [Google Website Translator](http://translate.google.com/manager/website/) and sign in with your Google account.
2.  Enter the URL for your website and the original language of your website.
3.  You can customize the plugin settings like _Translation languages (all/specific)_, _Display modes (Inline/Tabbed/Automatic)_ etc. and whether you want to track Translation Traffic using Google Analytics.
4.  Google translate will give you a `<meta>` tag and a `code snippet` with `<div>` and `<script>` tags.


#### Step 2 : Use the above snippet in your Octopress blog

1. Create a file `google_translate.html` in `source/_includes/`.
2. Add the `<meta>` tag and the `<script>` tags to this file.
```
<meta name="google-translate-customization" content="ABCDEFGHIJKLMNOPQRSTUVWXYZ"></meta>
<script type="text/javascript">
    function googleTranslateElementInit() {
        new google.translate.TranslateElement({
              pageLanguage: 'en', 
              layout: google.translate.TranslateElement.InlineLayout.SIMPLE,
              gaTrack: true,
              gaId: 'UA-ABCDEFGH'},
         'google_translate_element');
}
</script>

<script type="text/javascript" src="//translate.google.com/translate_a/element.js?cb=googleTranslateElementInit"></script>
```
If you observe carefully, you can look at the values of the parameters that you set when you got the code. e.g. I'm using _inline_ styling with _Simple_ layout and Google Analytics to track translation traffic.  
3. Add the following line where you want to display the plugin.  

```
<div id="google_translate_element">
```  
4. Include `google_translate.html` in the `<head>` tag in the file `source/_includes/head.html`, like [this](https://github.com/imathis/octopress/blob/master/.themes/classic/source/_includes/head.html#L28).  
5. Run `rake generate ; rake preview` to play with it locally on your machine.  

For more help, visit official [Google translate support page](https://support.google.com/translate/) or if you face any problems, mention so in the comments section.
