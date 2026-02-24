# #337 - Problem in running bootstrap.bat [Closed]

> Username: RezaSwe  
> Created at: 2018-09-03 19:14:05 UTC  
> Updated at: 2019-05-30 19:19:31 UTC  
> Closed at: 2019-04-01 18:10:55 UTC  
> Url: https://github.com/boostorg/build/issues/337  

Hi  
  
I am trying to run bootstrap.bat, but I got this error message:  
  
'cl' is not recognized as an internal or external command,  
operable program or batch file.  
  
Here also comes the log file:  
  
###  
### Using 'msvc' toolset.  
###  
  
C:\work\boost\tools\build\src\engine>if exist bootstrap rd /S /Q bootstrap   
  
C:\work\boost\tools\build\src\engine>md bootstrap   
  
C:\work\boost\tools\build\src\engine>cl /nologo /GZ /Zi /MLd /Fobootstrap/ /Fdbootstrap/ -DNT -DYYDEBUG kernel32.lib advapi32.lib user32.lib /Febootstrap\jam0  command.c compile.c constants.c debug.c execcmd.c execnt.c filent.c frames.c function.c glob.c hash.c hdrmacro.c headers.c jam.c jambase.c jamgram.c lists.c make.c make1.c object.c option.c output.c parse.c pathnt.c pathsys.c regexp.c rules.c scan.c search.c subst.c timestamp.c variable.c modules.c strings.c filesys.c builtins.c md5.c class.c cwd.c w32_getreg.c native.c modules/set.c modules/path.c modules/regex.c modules/property-set.c modules/sequence.c modules/order.c   
  
C:\work\boost\tools\build\src\engine>exit /b 9009   
  
MSVC version is 17.  
  
Thanks for your help in advance,  
Reza

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-09-03 19:29:30 UTC  
> Url: https://github.com/boostorg/build/issues/337#issuecomment-418181432  

AMDG  
  
On 09/03/2018 01:14 PM, RezaSwe wrote:  
> I am trying to run bootstrap.bat, but I got this error message:  
>   
> 'cl' is not recognized as an internal or external command,  
> operable program or batch file.  
>   
  
Try running from the visual studio command prompt.  
(This is not supposed to be necessary, but it sometimes  
helps with problem finding the compiler).  
  
> Here also comes the log file:  
>   
> <snip>  
> MSVC version is 17.  
>   
Please give the full version.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: RezaSwe  
> Created at: 2018-09-04 18:55:21 UTC  
> Url: https://github.com/boostorg/build/issues/337#issuecomment-418479221  

It worked! Thanks!  
  
The VS version is 15.9.0 Preview 1.0

---

## Comment 3

> Username: scharnk  
> Created at: 2019-05-30 19:19:31 UTC  
> Url: https://github.com/boostorg/build/issues/337#issuecomment-497451977  

I've been having the same issue, but using the Administrator Developer Command Prompt has not worked for VS2015, VS2017, or VS2019 (16.1.1) -- Anyone have any suggestions?
