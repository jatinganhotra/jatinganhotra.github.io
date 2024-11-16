---
layout: post
title: "Install MongoDB on Ubuntu"
date: 2011-11-15 01:33
comments: true
author: Jatin Ganhotra (@jatinganhotra)
categories: [Ubuntu, MongoDB, How to Install]
sharing: true
footer: true
---

<link href='http://fonts.googleapis.com/css?family=Coming+Soon&subset=latin,latin-ext' rel='stylesheet' type='text/css'>

<div>
<span style="float:right;" id="google_translate_element"></span>
<span style="float:right; font-family: 'Coming Soon', cursive;">Having trouble viewing in English, Choose Your Language : &nbsp;&nbsp;&nbsp;</span>
</div>
<BR>&nbsp;<BR>

<script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.6.4/jquery.min.js"></script>
<script src="{{ root_url }}/javascripts/jquery.accordion.js"></script>
<script src="{{ root_url }}/javascripts/jquery.easing.1.3.js"></script>
<script type="text/javascript">
  $(function() {			
    var $j = jQuery.noConflict();
    $j('#st-accordion').accordion({
      oneOpenedItem	: true
    });
  });
</script>

<div class="container">
There are multiple ways to install MongoDB on Ubuntu (I have got Ubuntu 11.10 setup on my machine).
<div class="wrapper">
    <div id="st-accordion" class="st-accordion">
    <ul>
      <li>
    <a href="#">the mongodb package in Debian/Ubuntu<span class="st-arrow">Open or Close</span></a>
    <div class="st-content">
        On running
        {% codeblock %}
        sudo apt-cache show mongodb
        {% endcodeblock %}
        it will print out a lot of information about the package like
```
Package: mongodb
Architecture: i386
Version: 1:1.8.2-1ubuntu1
Depends: mongodb-server, mongodb-dev
Filename: pool/universe/m/mongodb/mongodb_1.8.2-1ubuntu1_i386.deb
```
        If you carefully notice, the version is outdated. If you wish to install it, run
        {% codeblock %}
        sudo apt-get install mongodb
        {% endcodeblock %}
        and mongodb will be installed for you alongwith<br/><em>libmozjs185-1.0 mongodb-clients mongodb-dev mongodb-server</em>
        </div>
    </li>
    <li>
        <a href="#">the mongodb-10gen package by 10gen<span class="st-arrow">Open or Close</span></a>
        <div class="st-content">

        To install the mongodb-10gen package, corresponding to the latest stable release, follow the instructions as follows:<br/>
        
        + Add the GPG key:
        {% codeblock %}
        sudo apt-key adv --keyserver keyserver.ubuntu.com --recv 7F0CEB10
        {% endcodeblock %}

        + Add this line verbatim to your <strong>/etc/apt/sources.list</strong>
``` ruby /etc/apt/sources.list
# if you are on older Ubuntus
deb http://downloads-distro.mongodb.org/repo/debian-sysvinit dist 10gen
# On new Ubuntus, add this
deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen
```

        + Update the sources
        {% codeblock %}
        sudo apt-get update
        {% endcodeblock %}

        + Install the desired package(latest stable or latest development release)
        {% codeblock %}
        sudo apt-get install mongodb-10gen
        {% endcodeblock %}
            
        For more detailed guide on this, refer to <a href="http://www.mongodb.org/display/DOCS/Ubuntu+and+Debian+packages">MongoDB guide for Ubuntu and Debian packages</a>

        </div>
    </li>
		<li>
      <a href="#">Unix binaries<span class="st-arrow">Open or Close</span></a>
      <div class="st-content">
32-bit
``` javascript
$ # replace "2.0.1" in the url below with the version you want
$ curl http://downloads.mongodb.org/linux/mongodb-linux-i686-2.0.1.tgz > mongo.tgz
$ tar xzf mongo.tgz
```
64-bit
``` javascript
$ # replace "2.0.1" in the url below with the version you want
$ curl http://downloads.mongodb.org/linux/mongodb-linux-x86_64-2.0.1.tgz > mongo.tgz
$ tar xzf mongo.tgz
```          
      </div>
    </li>
		<li>
      <a href="#">Building from source<span class="st-arrow">Open or Close</span></a>
      <div class="st-content">
        + Install prerequisites
        For Ubuntu 10.04+ :<br/>
        (For older versions, refer <a href="http://www.mongodb.org/display/DOCS/Building+for+Linux"> MongoDB guide for building from source </a> )
``` javascript
apt-get -y install tcsh git-core scons g++
apt-get -y install libpcre++-dev libboost-dev libreadline-dev xulrunner-1.9.2-dev
apt-get -y install libboost-program-options-dev libboost-thread-dev libboost-filesystem-dev libboost-date-time-dev
```
        + Get source
``` javascript
git clone git://github.com/mongodb/mongo.git && cd mongo
git tag -l
git checkout r2.0.0
```
        + Build
        {% codeblock %}
        scons all
        {% endcodeblock %}
        + Install --prefix can be anywhere you want to contain your binaries e.g. /usr/local or /opt/mongo.
        {% codeblock %}
        scons --prefix=/opt/mongo install
        {% endcodeblock %}
    </div>
  </li>

            </ul>
        </div>
    </div>
</div>
<div>
<style type="text/css">
a{color:#000;text-decoration:none;}.wrapper{width:90%;max-width:800px;margin:30px auto;}.st-accordion{width:100%;min-width:270px;margin:0 auto;}.st-accordion ul li{height:100px;border-bottom:1px solid #c7deef;border-top:1px solid #fff;overflow:hidden;}.st-accordion ul li:first-child{border-top:none;}.st-accordion ul li > a{font-family:'Josefin Slab',Georgia, serif;text-shadow:1px 1px 1px #fff;font-size:24px;display:block;position:relative;line-height:100px;outline:none;-webkit-transition:color .2s ease-in-out;-moz-transition:color .2s ease-in-out;-o-transition:color .2s ease-in-out;-ms-transition:color .2s ease-in-out;transition:color .2s ease-in-out;}.st-accordion ul li > a span{background:transparent url({{ root_url }}/images/down.png) no-repeat center center;text-indent:-9000px;width:26px;height:14px;position:absolute;top:50%;right:-26px;margin-top:-7px;opacity:0;-webkit-transition:all .2s ease-in-out;-moz-transition:all .2s ease-in-out;-o-transition:all .2s ease-in-out;-ms-transition:all .2s ease-in-out;transition:all .2s ease-in-out;}.st-accordion ul li > a:hover span{opacity:1;right:10px;}.st-accordion ul li.st-open > a span{-webkit-transform:rotate(180deg);-moz-transform:rotate(180deg);transform:rotate(180deg);right:10px;opacity:1;}.st-content{padding:5px 0 30px;}.st-content p{font-size:16px;font-family:Georgia, serif;font-style:italic;line-height:28px;padding:0 4px 15px;}.st-content img{width:125px;border-right:1px solid #fff;border-bottom:1px solid #fff;}.st-accordion ul li > a:hover,.st-accordion ul li.st-open > a{color:#1693eb;}@media screen and max-width 320px{.st-accordion ul li > a{font-size:36px;}}
</style>
</div>
