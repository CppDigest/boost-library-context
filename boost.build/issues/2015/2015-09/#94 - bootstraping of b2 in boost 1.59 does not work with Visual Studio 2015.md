# #94 - bootstraping of b2 in boost 1.59 does not work with Visual Studio 2015 [Closed]

> Username: strentler  
> Created at: 2015-09-04 06:14:59 UTC  
> Updated at: 2017-10-21 19:49:29 UTC  
> Closed at: 2015-09-10 18:58:51 UTC  
> Url: https://github.com/boostorg/build/issues/94  

Here a snippet out of bootstrap.log:  
###   
### Using 'vc14' toolset.  
###   
  
e:\boost\boost_git\modular-boost\tools\build\src\engine>if exist bootstrap rd /S /Q bootstrap   
  
e:\boost\boost_git\modular-boost\tools\build\src\engine>md bootstrap   
  
e:\boost\boost_git\modular-boost\tools\build\src\engine>cl /nologo /RTC1 /Zi /MTd /Fobootstrap/ /Fdbootstrap/ -DNT -DYYDEBUG -wd4996 kernel32.lib advapi32.lib user32.lib /Febootstrap\jam0  command.c compile.c constants.c debug.c execcmd.c execnt.c filent.c frames.c function.c glob.c hash.c hdrmacro.c headers.c jam.c jambase.c jamgram.c lists.c make.c make1.c object.c option.c output.c parse.c pathnt.c pathsys.c regexp.c rules.c scan.c search.c subst.c timestamp.c variable.c modules.c strings.c filesys.c builtins.c md5.c class.c cwd.c w32_getreg.c native.c modules/set.c modules/path.c modules/regex.c modules/property-set.c modules/sequence.c modules/order.c   
command.c  
e:\boost\boost_git\modular-boost\tools\build\src\engine\jam.h(34): fatal error C1083: Cannot open include file: 'ctype.h': No such file or directory  
compile.c  
e:\boost\boost_git\modular-boost\tools\build\src\engine\jam.h(34): fatal error C1083: Cannot open include file: 'ctype.h': No such file or directory  
constants.c  
debug.c  
e:\boost\boost_git\modular-boost\tools\build\src\engine\jam.h(34): fatal error C1083: Cannot open include file: 'ctype.h': No such file or directory  
execcmd.c

---

## Comment 1

> Username: vprus  
> Created at: 2015-09-04 09:18:52 UTC  
> Url: https://github.com/boostorg/build/issues/94#issuecomment-137686244  

Bootstrap invokes vcvarsall.bat to set all paths. If, after that, cl.exe cannot open ctype.h, this suggests Visual Studio installation problem.Apparently, others ran into this as well:  
http://stackoverflow.com/questions/31862627/vs2015-cl-cant-find-crt-libs-stido-h-ctype-h-etc-when-building-on-command-l  
  
Does compiling of .cpp files works on command line for you?

---

## Comment 2

> Username: strentler  
> Created at: 2015-09-04 09:37:13 UTC  
> Url: https://github.com/boostorg/build/issues/94#issuecomment-137690855  

It is the same problem here like described in the stackoverflow post. Only happens when Windows Driver Framework is installed.  
Workaround:Rename: "c:\Program Files (x86)\Windows Kits\10\Include\wdf"to this:"c:\Program Files (x86)\Windows Kits\10\Include\00wdf"   
Date: Fri, 4 Sep 2015 02:18:54 -0700  
From: notifications@github.com  
To: build@noreply.github.com  
CC: strentler@live.com  
Subject: Re: [build] bootstraping of b2 in boost 1.59 does not work with Visual Studio 2015 (#94)  
  
Bootstrap invokes vcvarsall.bat to set all paths. If, after that, cl.exe cannot open ctype.h, this suggests Visual Studio installation problem.Apparently, others ran into this as well:  
  
http://stackoverflow.com/questions/31862627/vs2015-cl-cant-find-crt-libs-stido-h-ctype-h-etc-when-building-on-command-l  
  
Does compiling of .cpp files works on command line for you?  
  
—  
Reply to this email directly or view it on GitHub.

---

## Comment 3

> Username: vprus  
> Created at: 2015-09-04 09:54:55 UTC  
> Url: https://github.com/boostorg/build/issues/94#issuecomment-137693396  

So, what do you suggest Boost.Build do? It's not like we can go go and rename folders for the user?

---

## Comment 4

> Username: strentler  
> Created at: 2015-09-04 10:13:38 UTC  
> Url: https://github.com/boostorg/build/issues/94#issuecomment-137696112  

From what i have found out, there is an issue on microsoft connect. Microsoft wants to fix this prolem. Its not a bug of boost.build  
https://connect.microsoft.com/VisualStudio/feedback/details/1610302/universalcrt-detection-breaks-when-windows-driver-kit-is-installed  
  
Date: Fri, 4 Sep 2015 02:54:56 -0700  
From: notifications@github.com  
To: build@noreply.github.com  
CC: strentler@live.com  
Subject: Re: [build] bootstraping of b2 in boost 1.59 does not work with Visual Studio 2015 (#94)  
  
So, what do you suggest Boost.Build do? It's not like we can go go and rename folders for the user?  
  
—  
Reply to this email directly or view it on GitHub.

---

## Comment 5

> Username: vprus  
> Created at: 2015-09-10 18:58:51 UTC  
> Url: https://github.com/boostorg/build/issues/94#issuecomment-139345106  

Given your last comment, I will close the issue.

---

## Comment 6

> Username: ozanaral  
> Created at: 2015-11-09 20:03:21 UTC  
> Url: https://github.com/boostorg/build/issues/94#issuecomment-155175409  

I have exactly the same bootstrap.log. So if the issue is because of the windows sdk (that i have it installed on my computer) would uninstalling the windows sdk solve the issue?

---

## Comment 7

> Username: strentler  
> Created at: 2015-11-09 20:38:58 UTC  
> Url: https://github.com/boostorg/build/issues/94#issuecomment-155186115  

Do you have Windows Driver Framework installed?

---

## Comment 8

> Username: ozanaral  
> Created at: 2015-11-11 10:13:29 UTC  
> Url: https://github.com/boostorg/build/issues/94#issuecomment-155722575  

yes, sorry, i meant to say "Windows Driver Framework" instead of "windows sdk". I've been able to install boost successfully by changing the folder "wdf" to "00wdf" but now my Visual Studio and some other programs on my pc are working painfully slow. Is it because now VS is processing all the folders inside the boost directory or the WDF? Also, would uninstalling WDF avoid any possible bad interference with my VS? I downloaded the windows framework out of curiosity i don't really need it

---

## Comment 9

> Username: dolfjaapklomp  
> Created at: 2017-10-16 19:46:12 UTC  
> Updated at: 2017-10-16 19:46:31 UTC  
> Url: https://github.com/boostorg/build/issues/94#issuecomment-337014406  

I ran into what seems to be the same problem with building boost 1.65.1 with visual studio 2017 installed. For me the workaround was running the bootstrap from the "Visual Studio Developer Command Prompt" of course in administrator mode.

---

## Comment 10

> Username: iurybakov  
> Created at: 2017-10-21 19:49:28 UTC  
> Url: https://github.com/boostorg/build/issues/94#issuecomment-338427892  

@dolfjaapklomp thanks! Case VS2017 and boost 1.65.1. I had an error with direct start bootstrap.bat:  
"fatal error C1083: Cannot open source file: 'yyacc.c': No such file or directory".  
In Visual Studio Developer Command Prompt (administrator mode) bootstrap.bat worked correctly.
