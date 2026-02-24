# #573 WIP: Add colors support to echo rule and actions [Closed]

> Username: mloskot  
> Created at: 2020-04-21 00:32:22 UTC  
> Updated at: 2021-10-02 20:59:42 UTC  
> Closed at: 2021-03-27 11:45:56 UTC  
> Url: https://github.com/boostorg/build/pull/573  

This is my first stab at native support for colourful output from the built-in `ECHO` rule and other sources like actions (name, target, command).  
  
It's been long time since I brainstormed this idea, https://svn.boost.org/trac10/ticket/3508 and https://lists.boost.org/boost-build/2012/01/25582.php, then implemented it as Perl script filter https://github.com/mloskot/colorbb, but lately, I've started looking into B2 source code more. Inspired by @jpcima's #261 and @swatanabe's https://github.com/boostorg/build/issues/261#issuecomment-352288362 , I picked it up and _experimented_ a bit.  
  
It is not a feature-complete optimal implementation, but an experimental proposal to collect feedback about direction of this feature. It is working and usable though, on both, Windows and Unix. It uses basic set of [8/16 ANSI colors](https://misc.flogisoft.com/bash/tip_colors_and_formatting).  
  
The outout is only coloured if requested specifying the `-c` option in the command line.  
  
### Examples: Windows  
  
I used ConEmu w/ Monokai color scheme on Windows.  
  
Initial examples of the colourful output from the `ECHO`, see the `example/colors/Jamfile`, built with `./b2 -c`  
  
![image](https://user-images.githubusercontent.com/80741/79811123-3877a980-8375-11ea-9960-1b88a3f9f87e.png)  
  
![image](https://user-images.githubusercontent.com/80741/79811574-88a33b80-8376-11ea-90e2-83832ba5f594.png)  
  
and built with `./b2 -c -d+2`  
  
![image](https://user-images.githubusercontent.com/80741/79811629-b2f4f900-8376-11ea-9b44-1b7fa237ba26.png)  
  
![image](https://user-images.githubusercontent.com/80741/79811662-cef89a80-8376-11ea-9eaf-c7956494b2ac.png)  
  
### Examples: Linux  
  
![image](https://user-images.githubusercontent.com/80741/79892851-6dcad880-8403-11ea-9e59-4d101f2235c4.png)  
  
![image](https://user-images.githubusercontent.com/80741/79892945-9ce14a00-8403-11ea-8082-3fd89c701304.png)  
  
------  
  
(The CMake files bundled in the first commit are just for temporary convenience, for myself and others who may find it useful for testing of this PR. If this PR ever becomes ready to merge, all the CMake cruft will be removed.)

---

## Comment 1

> Username: eldiener  
> Created_at: 2020-04-21 17:34:21 UTC  
> Url: https://github.com/boostorg/build/pull/573#issuecomment-617307280  

Personally I always 'tee' the b2 output to a file, which I then view in my editor, JEdit. But maybe others will find your "colorful" b2 output helpful.

---

## Comment 2

> Username: mloskot  
> Created_at: 2020-04-21 17:57:29 UTC  
> Url: https://github.com/boostorg/build/pull/573#issuecomment-617319969  

@eldiener   
> Personally I always 'tee' the b2 output to a file (...) maybe others will find your "colorful" b2 output helpful.  
  
I guess, you are not finding colours from GCC or clang helpful either. No worry, I'm proposing opt-in colouring.  
  
FYI, use of `tee` can be replaced with `-o` option: `b2 -olog.txt` which tells `b2` to mirror in `log.txt` file everything it sends to `stdout` and `stderr`.

---

## Comment 3

> Username: eldiener  
> Created_at: 2020-04-21 18:04:40 UTC  
> Url: https://github.com/boostorg/build/pull/573#issuecomment-617323849  

Thanks for the information about the b2 -o option.

---

## Comment 4

> Username: HDembinski  
> Created_at: 2020-04-22 15:04:49 UTC  
> Url: https://github.com/boostorg/build/pull/573#issuecomment-617835579  

I love this very much.

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2020-04-22 15:21:32 UTC  
> Url: https://github.com/boostorg/build/pull/573#issuecomment-617845708  

LGTM

---

## Comment 6

> Username: mloskot  
> Created_at: 2020-04-22 20:12:16 UTC  
> Url: https://github.com/boostorg/build/pull/573#issuecomment-618013835  

@grafikrobot I have two issues which I'd like to reach your opinion about  
  
### What is preferred `ECHO` argument format for colors?  
  
I [brainstormed variants on the Slack](https://cpplang.slack.com/archives/C27KZLB0X/p1587069125476300).  
  
Basically, is it better to have single dedicated argument for all colors (and their hyphen-delimited attributes):  
  
```  
ECHO "red on default" : red ;  
ECHO "red on blue" : red blue ;  
```  
  
or better is to split the foreground and background into two separate formal arguments:  
  
```  
ECHO "red on blue" : red : blue ;  
```  
  
### Do we want bold text too or just colors (normal and light)?  
  
I'm going to clean up handling of the internal flags for colors to better support light colors on **Unix** terminals using dedicated escape codes.  
  
For example, currently, red is `\e[31` and additional attribute `\e[1` will make it brighter but also text becomes bold. This needs to become `\e[31`  for red , `\e[91` for light red, etc. to allow this without affecting text:  
  
```  
ECHO "red on blue" : red-light blue ;  
ECHO "red on blue" : red blue-light;  
```   
  
Then, the `\e[1`code could control bold text separately, for example:  
  
```  
ECHO "red on blue" : red-bold ;  
ECHO "red on blue" : red-light-bold blue ;  
```   
  
Question is, is bold attribute really useful or needed?

---

## Comment 7

> Username: grafikrobot  
> Created_at: 2020-04-22 20:20:59 UTC  
> Url: https://github.com/boostorg/build/pull/573#issuecomment-618018249  

> @grafikrobot I have two issues which I'd like to reach your opinion about  
> or better is to split the foreground and background into two separate formal arguments:  
>   
> ```  
> ECHO "red on blue" : red : blue ;  
> ```  
  
I would lean towards this. Because...  
  
> ### Do we want bold text too or just colors (normal and light)?  
  
Probably, yes.  
  
> I'm going to clean up handling of the internal flags for colors to better support light colors on **Unix** terminals using dedicated escape codes.  
>   
> For example, currently, red is `\e[31` and additional attribute `\e[1` will make it brighter but also text becomes bold. This needs to become `\e[31` for red , `\e[91` for light red, etc. to allow this without affecting text:  
>   
> ```  
> ECHO "red on blue" : red-light blue ;  
> ECHO "red on blue" : red blue-light;  
> ```  
>   
> Then, the `\e[1`code could control bold text separately, for example:  
>   
> ```  
> ECHO "red on blue" : red-bold ;  
> ECHO "red on blue" : red-light-bold blue ;  
> ```  
>   
> Question is, is bold attribute really useful or needed?  
  
I can see using it in the `--help` output. And the most modular argument handling I can see is to have:  
  
```  
ECHO "whatever" : bold red : white ;  
ECHO "whatever" : light blue ;  
ECHO "whatever" : yellow light bold : light blue ;  
```

---

## Comment 8

> Username: Kojoley  
> Created_at: 2020-04-23 00:27:30 UTC  
> Url: https://github.com/boostorg/build/pull/573#issuecomment-618107660  

I know I could try it myself but it is easier to ask. Does this paint compile fail test output (fail-as-expected) in red?

---

## Comment 9

> Username: mloskot  
> Created_at: 2020-04-23 11:48:02 UTC  
> Updated_at: 2020-04-23 22:53:48 UTC  
> Url: https://github.com/boostorg/build/pull/573#issuecomment-618355722  

@Kojoley ~it should paint it green~ The output is red, as it is an actual compilation error, but the  status `fail-as-expected` is green as it's a successful result, as below  
  
![image](https://user-images.githubusercontent.com/80741/80157174-dcf02a80-85c5-11ea-8cf4-9b8209dc07cc.png)

---

## Comment 10

> Username: mloskot  
> Created_at: 2020-04-23 22:58:32 UTC  
> Url: https://github.com/boostorg/build/pull/573#issuecomment-618713466  

@grafikrobot Excellent, the modular arguments handling is what I also prefer. I initially went for squeezing all colors into single argument in case `ECHO` gets other arguments in future. Then, it may be better to pass all colors stuff as single one.  
  
I will make necessary changes to this PR soon.

---

## Comment 11

> Username: chambm  
> Created_at: 2020-06-24 16:24:39 UTC  
> Updated_at: 2020-06-24 16:25:25 UTC  
> Url: https://github.com/boostorg/build/pull/573#issuecomment-648924406  

I'd love to have colors for my Windows builds. I actually tried to do this a few years ago but failed, so I'm glad you got it working. Does it work in cmd? Can we get it to colorize lines conditionally like coloring warnings, errors, and notes distinctly even if they're from the same compile command?

---

## Comment 12

> Username: mloskot  
> Created_at: 2020-06-25 08:20:23 UTC  
> Url: https://github.com/boostorg/build/pull/573#issuecomment-649359148  

@chambm   
> Does it work in cmd?   
  
Yes, it works in the Windows Command Prompt too.  
  
> Can we get it to colorize lines conditionally like coloring warnings, errors, and notes distinctly even if they're from the same compile command?  
  
In this first stab, I focused on the basic framework, but I think I've achieve a basic implementation of something like that.  
For fine grained colouring,  I'll have to dig it deeper into the engine implementation, what I am going to do.  
  
FYI, this is PR is not abandoned. I had to put it on backburner as unexpected duties due to COVID19 have been eating all my free time.

---

## Comment 13

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:41 UTC  
> Url: https://github.com/boostorg/build/pull/573#issuecomment-932819682  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
