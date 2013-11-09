```
type: post
layout: post
title: Octopress Windows-Style
date: 2013-11-06 22:00
tags: ['octopress']
```

I would say this is another one of those _"moving to Octopress"_ posts, except that I only had one post and my WordPress blog had not been active a full 24 hours when I switched. So moving my content was a matter of _ctrl-a, ctrl-c, alt-tab, ctrl-v_.<!-- read more -->

Instead post will talk about how to run Octopress on Windows, since for obvious reasons most of the available information is for fowl-based operating systems or fruit-based computers.

It might just be that it's so simple to do on Windows that no one has felt the need to write about it. But hey, maybe that'll be my niche, I'll write about all the really simple stuff that no one else thinks is worth the keystrokes.

This post also assumes that you have none of the required tools installed, so we're starting with...

##Installing Git##

Head on over [here](http://git-scm.com/download/win) and your download should start automatically. Run the installer, it's not a very scary one.

When the installer asks you about adjusting your PATH environment, pick at least _**Run Git from the Windows Command Prompt**_. The remainder of this setup assumes that you have. 

![git install dialog](/images/git-install.png)

_**If you have previously installed Git and did not have the installer set the PATH variable you might want to do it manually now**_

Side note: If command prompt Git scares you or you just like great applications, I would recommend you check out [SourceTree](http://www.sourcetreeapp.com/). It is all kinds of awesome and it's [git-flow](http://github.com/nvie/gitflow) friendly and free!


##Installing Ruby and DevKit##

_Edit: You might want to check out [this post](/blog/2013/11/07/multiple-rubies-on-windows/) for installing Ruby and DevKit instead, or you can just keep going._

- Go [here](http://rubyinstaller.org/downloads/) and select the Ruby installer of your choice. (Octopress requires version 1.9.3 or later at the time of writing this post)
- Make sure that you **do not include any white space in the installation path** as this apparently can have adverse effects and the same goes for DevKit.
- Check _**Add Ruby executables to your PATH**_ _(note that Ruby sets your user specific PATH and not the system one)_

![ruby install dialog](/images/ruby-install.png)  
_The other options you can do as you see fit, I'd recommend checking them both_

- While Ruby is installing you want to start downloading the DevKit from the [same page as before](http://rubyinstaller.org/downloads/), taking care to read which version of the DevKit goes with your version of Ruby.
- The DevKit is delivered as a self-extracting archive and where you extract it is where it will be installed; So pick a good spot. I generally put it somewhere under the directory of my ruby installation.

Open up PowerShell and run the following

```
PS C:\> cd ruby\193\devkit

PS C:\ruby\193\devkit> ruby .\dk.rb init
[INFO] found RubyInstaller v1.9.3 at C:/ruby/193
-- output snipped ---

PS C:\ruby\193\devkit> ruby .\dk.rb install
[INFO] Updating convenience notice gem override for 'C:/ruby/193'
[INFO] Installing 'C:/ruby/193/lib/ruby/site_ruby/devkit.rb'
```

##You're ready for Octopress!##

Start by cloning the Octopress project from Github    
_Note that D:\Git is not where Git was installed, but where I store all of my git repositories_

```
PS D:\Git> git clone git://github.com/imathis/octopress.git octopress
Cloning into 'octopress'...
--- output snipped ---
Checking connectivity... done
```

Then move into the newly created repository

```
PS D:\Git\> cd octopress
```

and run the following commands

```
PS D:\Git\octopress> gem install bundle
--- output snipped ---
2 gems installed

PS D:\Git\octopress> bundle install
--- lots of output snipped ---
Your bundle is complete!
Use `bundle show [gemname]` to see where a bundled gem is installed.
```

If you run into issues during the two previous steps it most likely means that you didn't install the DevKit or that your environment PATH is not set up correctly. You might want to check that none of the paths in your PATH variable is wrapped in quotes as PowerShell does not load these. Otherwise you can try rerunning from Installing Ruby, it won't harm anything.

You might also have problems if you had a different version of ruby installed already. In which case you might have to install [pik](http://github.com/vertiginous/pik) to manage your ruby versions, but that's a whole other post.

Finally, install the classic theme (or find yourself a different one in the configuration )  

``` html
 PS D:\Git\octopress> bundle exec rake install
 ## Copying classic theme into ./source and ./sass
 --- output snipped ---
```

_The 'bundle exec' part is only required if there are gem version conflicts, which at the time of this writing there would only be if you installed 2.0.0 instead of 1.9.3_

Congratulations! installation is done and you're ready to [configure Octopress](http://octopress.org/docs/configuring/).

Bon Voyage!
