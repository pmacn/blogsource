---
type: post
layout: post
title: Setting up multiple rubies on Windows
date: 2013-11-07 10:47
tags: ['ruby']
---

This post will deal with getting a solid installation of multiple Rubies on Windows. If you've got previous installations you can always just uninstall them and start over from here. Make sure to delete previous installation folders after uninstalling.<!-- read more -->

##Installing Rubies##

Go get all the [ruby installers](http://rubyinstaller.org/downloads/) you want and install each one into their own folder. _(eg. C:\Ruby\Ruby193 or C:\Ruby\Ruby200-x64)_

Select one as your default version and install it with _**Add Ruby executables to your PATH**_.  
This will be the active version when opening a new PowerShell.

##Installing DevKits##

Download all of the DevKits required for your different Ruby installs from the [same page](http://rubyinstaller.org/downloads/). It should be fairly straight forward to figure out which ones you need.

While downloading the DevKits you can open up a PowerShell and set the [execution policy](http://technet.microsoft.com/library/hh847748.aspx) so that you can run scripts. This isn't needed until later but we might as well get it out of the way.

``` bash
PS C:\> Set-ExecutionPolicy Unrestricted -Scope CurrentUser
```

_**Repeat the following procedure for each DevKit that you are installing**_

Extract the DevKit to its own folder. _(e.g. C:\Ruby\DevKit-mingw64-4.7.2 or C:\Ruby\DevKit-tdm-32-4.5.2\)_

Back to our PowerShell so we can initialize the DevKit installation

``` bash
PS C:> cd Ruby\DevKit-mingw64-4.7.2
PS C:\Ruby\DevKit-mingw64-4.7.2> ruby .\dk.rb init  
```

Chances are that the initialization didn't go as intended so open up config.yml in a text editor. (yay notepad!)

``` bash
PS C:\Ruby\DevKit-mingw64-4.7.2> notepad config.xml
```

The config file should contain the installation paths of the Ruby versions that this DevKit is for. Add any missing ones and remove any that are not for that DevKit.  
_Note the forward-slashes and the leading dash and space_

For instance, if the DevKit is for 2.0.0 x64

``` bash
- C:/Ruby/Ruby200-x64
```

or if the DevKit is for 1.8.7 and 1.9.3

``` bash
- C:/Ruby/Ruby187
- C:/Ruby/Ruby193
```

Once you've made sure that the file contains the right paths, save any changes and close notepad.

Then you can finally install the DevKit

``` bash
PS C:\Ruby\DevKit-mingw64-4.7.2> ruby .\dk.rb install
```

_And now to the hero of our little drama..._
#Pik to the rescue!#
We begin by installing the Pik gem

``` bash
PS C:\> gem install pik
--- output snipped ---
1 gem installed
```

Now find a good spot for Pik to live and install it there (I usually put it in C:\Ruby\Pik)

``` bash
PS C:\> pik_install C:\Ruby\Pik
```

At this point Pik will warn you that the installation path is not in your PATH variable, so lets fix that.

``` bash
    PS C:\> [environment]::SetEnvironmentVariable("PATH", "C:\Ruby\Pik;" + [environment]::GetEnvironmentVariable("PATH", "Machine"), "Machine")
```

_Since that's quite a mouthful you might want to create a [function](/posts/hardest-way-to-set-a-path/) for that some day_

Now that Pik is ready to go, lets see what it has to say

``` bash
PS C:> pik info
pik 0.2.8

ruby:
interpreter:  "ruby"
version:      "1.9.3"
--- output snipped ---
```

If Pik complains that you do not have a ruby version in your PATH you might want to go add one now for the version that you want to be your default.  
If Pik instead warns you that you have more than one Ruby installation in your PATH variable, you should go remove all but the one you want as you default.  
This is why we only installed one of the Ruby versions with the _Add Ruby executables to your PATH_ option.

If you have installed pik before you might see something like this?

<code>There was an error.  
Error: undefined method `[]' for nil:NilClass</code>

If this is the case, remove your Ruby installations from Pik and re-add them. To see installations already added to Pik use the list command.

``` bash
PS C:> pik list
* 193: ruby 1.9.3p448 (2013-06-27) [i386-mingw32]
```

Then remove and add them

``` no-highlight
PS C:> pik rm 193
Are you sure you'd like to remove '193: ruby 1.9.3p448 (2013-06-27) [i386-mingw32]'?  |yes|
yes
193: ruby 1.9.3p448 (2013-06-27) [i386-mingw32] removed.

PS C:> pik add C:\Ruby\Ruby193\bin
** Adding:  193: ruby 1.9.3p448 (2013-06-27) [i386-mingw32]
Located at:  C:\Ruby\Ruby193\bin
```

Once <code>pik info</code> returns actual information about one of your ruby installations, you can add the other ones.  
When you have added all your ruby installations to pik you can switch versions like this.

``` bash
PS C:\> pik use 200
```

And you're good to go, for now.
