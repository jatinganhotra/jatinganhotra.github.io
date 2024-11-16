---
layout: post
title: "Installing Octave on OS X 10.9 Mavericks"
date: 2014-01-21 21:25
comments: true
author: Jatin Ganhotra (@jatinganhotra)
categories: [Mac OS X, Octave, How to Install]
sharing: true
footer: true
---

<link href='http://fonts.googleapis.com/css?family=Coming+Soon&subset=latin,latin-ext' rel='stylesheet' type='text/css'>

<div>
<span style="float:right;" id="google_translate_element"></span>
<span style="float:right; font-family: 'Coming Soon', cursive;">Having trouble viewing in English, Choose Your Language : &nbsp;&nbsp;&nbsp;</span>
</div>
<BR>&nbsp;<BR>

If you upgraded to OS X 10.9 Mavericks and have started to like the latest enhancements in Finder, new iBooks and Maps applications etc., installing Octave is going to make you forget all of it. The installation process is a herculean task and you better pray to God that you don't run into much issues like I did.

I found decent help from here - [Getting Octave to Just Goddamn Work Already on OS X 10.8 Mountain Lion](http://ntraft.com/getting-octave-to-work-on-mountain-lion/), thanks to [Neil Traft](http://ntraft.com) for putting it together.  

But that's only half of the story as you've upgraded to OS X 10.9 Mavericks. Here, I am writing down steps that you should follow in the exact order and also tips to solve issues all by yourself, if you run into any.

__P.S.__ _Please don't ignore any warnings or any messages that you feel might be irrelevant. If one piece doesn't fit well, the whole thing may fall later._

#### 1. Install the Xcode Command Line Tools  
Installing command line tools in OSX 10.9 Mavericks has changed slightly than previous versions of MAC OS X. Now you don't have to install Xcode , you can install Command Line Tools stand alone.  
Type `xcode-select --install` in terminal and choose _Install_ option from the pop-up.  
This may take some time depending on your Internet connection.  

#### 2. Install Homebrew  
I'm assuming you know about Homebrew, but if you don't do yourself a favor, my friend and install it by following [Homebrew's installation instructions](https://github.com/mxcl/homebrew/wiki/installation).  

#### 3. Install XQuartz  
Install XQuartz from [here](https://xquartz.macosforge.org/landing/). In the past this came installed on OS X, but as of Mountain Lion it is no longer included. So go fix that.  

#### 4. Updating and upgrading Homebrew  
If you had Homebrew installed already, update to the latest package definitions. Don't skip this step out of mere laziness.  
`$ brew update && brew upgrade`  
This may take some time. So, go get yourself a cup of coffee and better have some beer. The path ahead could be tough.

#### 5. Import the scientific computing packages  
`$ brew tap homebrew/science`  
If you see any warnings here, run `brew doctor` and follow what it says. Let me repeat. Don't be smart and just do what the doctor says!!! The doctor is always right.  
Once you have followed the steps specifed by `brew doctor`, run  
`$ brew untap homebrew/science` and rerun  
`$ brew tap homebrew/science`.  

#### 6. Install gfortran  
To install Octave, you have to install `gfortran` first.  
`$ brew install gfortran`  
Pay close attention to any messages and warnings during the install. If everything looks good, jump to the next Step.  

If you see any warnings like: `/usr/bin/install_name tool: malformed object (unknown load command)`,  
go and visit this [Github issue](https://github.com/Homebrew/homebrew/issues/25665) that I raised for more details.  

If you're facing any other problem:  
  * run `brew doctor` first and do what the doctor says!!!  
  * Re-run the command again and see if the issue got resolved.  
  * If not, go ahead and raise a new issue at [Homebrew Issues](https://github.com/Homebrew/homebrew/issues).  

#### 7. Installing Octave  
`$ brew install octave`  
If everything looks good, jump to the next Step.  

If brew complains about not having a formula for octave, the following command should fix it:
`$ brew tap --repair`  

If you get an error like `do a web search for TeX and your operating system. make[3]: *** [octave.dvi] Error 1` then build with  
`$ brew install octave --without-docs` option.  
(_Thanks Pierre and Kahini for the correction about `--without-docs`, previously `-without-docs`_).  

This is because you don't have the Tex package installed. Alternatively, you can also download the 2.3GB _MacTeX.pkg_ from [here](http://tug.org/mactex/), install it and rerun the install command.  
(__Update:__ Don't try this !!! I installed MacTex package but the Octave installation still failed. I raised an issue here - [Homebrew-science Issues](https://github.com/Homebrew/homebrew-science/issues/796) but with no help.)

If you run into any other issue, run `brew doctor` first and do what the doctor says!!! Re-run the command again and   see if the issue got resolved. If not, go ahead and raise a new issue at [Homebrew-science Issues](https://github.com/Homebrew/homebrew-science/issues).  

#### 8. Install gnuplot  
For native drawing support, we'll install gnuplot.  
`$ brew install gnuplot`  
(__Update:__ As _phseven_ pointed out, gnuplot will be automatically installed with octave, but in some cases it won't support X11.) For such cases, do the following:  
`$ brew uninstall gnuplot`  
`$ brew install gnuplot --with-x`  

#### 9. Setup Octave startup file  
Edit your Octave startup file or create a new one:  
`$ vim ~/.octaverc` and add the line `setenv GNUTERM x11`.  
Don't forget to save the file.  

#### 10. Create a launcher app with Applescript [_Optional_]  
Open the "AppleScript Editor" application and write the following text in the editor window:
```
tell application "Terminal"
 do script "`which octave`; exit"
end tell
```
Save it as an application 'Octave.app' and then navigate it to the "Applications" folder.  

Now for the ultimate fix: Reboot your machine!!! Yep, until you restart your Mac, Octave will be unable to launch XQuartz on its own, and you won't be able to plot anything.  

Once you're back in, open the terminal, fire up Octave, and plot a _sombrero()_.__ You're all set!!!__
