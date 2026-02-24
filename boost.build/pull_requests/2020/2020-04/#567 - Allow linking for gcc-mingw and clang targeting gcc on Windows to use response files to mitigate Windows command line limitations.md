# #567 Allow linking for gcc/mingw and clang targeting gcc on Windows to use response files to mitigate Windows command line limitations. [Merged]

> Username: eldiener  
> Created at: 2020-04-15 03:23:49 UTC  
> Updated at: 2021-10-02 21:01:47 UTC  
> Merged at: 2020-06-10 00:12:27 UTC  
> Closed at: 2020-06-10 00:12:27 UTC  
> Url: https://github.com/boostorg/build/pull/567  



---

## Comment 1

> Username: eldiener  
> Created_at: 2020-04-16 15:20:42 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-614719528  

In the tests I see a failed link when cross-linking from Windows to Linux but I do not see in the tests any output which gives a reason for the failed link. Therefore it is impossible to determine what is wrong with my PR, which looks correct to me.

---

## Comment 2

> Username: eldiener  
> Created_at: 2020-04-21 01:19:51 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-616893550  

The problem for the toolset_clang_linux test, when run on Windows, is that the default clang on Windows target triple '-target' parameter when running a 64-bit compile is 'x86_64-pc-windows-msvc' and when running a 32-bit compile is 'i386-pc-windows-msvc '.  In other words the clang implementation on Windows defaults to targeting VC++ and not gcc, while the clang-linux.jam file is for linux, usually targeting gcc. The correct target triples for targeting gcc are 'x86_64-pc-windows-gnu' and 'i686-pc-windows-gnu'. But the clang-linux.jam does not attempt to set this correct target under Windows. Maybe it should be doing that but it never has, and I did not write the original clang-linux.jam file. Nor has my PR changed that aspect of the jam file at all. Of course on Linux the default target triple is for gcc, not msvc, so no target triple needs to be specified   
  
What is happening when the target triple is targeting vc++ on Windows is that when a linker line has '-Wl,-soname -Wl,libl1.so'. the -soname option is ignored and the linker then looks for 'libl1.so' as a source, which of course does not exist and it fails. Also of course stuff like '-Wl,--start-group -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group' it also ignores. With the correct target triple it knows what all this means.  
  
The only thing I am wondering is why this test_clang_linux test ever worked on Windows !!!

---

## Comment 3

> Username: eldiener  
> Created_at: 2020-04-23 00:04:10 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-618100832  

Please ignore the previous comment. Here is the actual issue, which is a Boost Build test system bug:  
  
If I add a response file to a command in the clang-linux.jam file the Boost Build test system test 'toolset_clang_linux' will give an error, despite the fact that response files are fully supported by clang. For instance with these lines in the current clang-linux.jam on the 'develop' branch:  
  
```  
actions link bind LIBRARIES {  
   "$(CONFIG_COMMAND)" -L"$(LINKPATH)" -Wl,-R$(SPACE)-Wl,"$(RPATH)" -Wl,-rpath-link$(SPACE)-Wl,"$(RPATH_LINK)" -o "$(<)" $(START-GROUP) "$(>)" "$(LIBRARIES)" $(FINDLIBS-ST-PFX) -l$(FINDLIBS-ST) $(FINDLIBS-SA-PFX) -l$(FINDLIBS-SA) $(END-GROUP) $(OPTIONS) USER_OPTIONS)  
}  
```  
  
replaced by:  
  
```  
actions link bind LIBRARIES {  
  "$(CONFIG_COMMAND)" -L"$(LINKPATH)" -Wl,-R$(SPACE)-Wl,"$(RPATH)" -Wl,-rpath-link$(SPACE)-Wl,"$(RPATH_LINK)" -o "$(<)" $(START-GROUP) "$(>)" "$(LIBRARIES)" $(FINDLIBS-ST-PFX) -l$(FINDLIBS-ST) $(FINDLIBS-SA-PFX) -l$(FINDLIBS-SA) $(END-GROUP) @"@($(<[1]).rsp:E=$(OPTIONS))" $(USER_OPTIONS)  
}  
```  
  
the Boost Build test system when running the 'toolset_clang_linux' will err with:  
  
```  
file bin\clang-linux-3.9.0\debug\target-os-linux\test.rsp  
-fPIC -g  
clang-linux.link bin\clang-linux-3.9.0\debug\target-os-linux\test  
  
  "C:\Utilities\python\x64\2.7.16\python.exe" "C:\Users\eldnew8\AppData\Local\Temp\tmplhdgla/src/clang-linux-3.9.0.py"  -Wl,-R -Wl,"C:\Users\eldnew8\AppData\Local\Temp\tmplhdgla\bin\clang-linux-3.9.0\debug\target-os-linux" -Wl,-rpath-link -Wl,"C:\Users\eldnew8\AppData\Local\Temp\tmplhdgla\bin\clang-linux-3.9.0\debug\target-os-linux" -o "bin\clang-linux-3.9.0\debug\target-os-linux\test" -Wl,--start-group "bin\clang-linux-3.9.0\debug\target-os-linux\main.o" "bin\clang-linux-3.9.0\debug\target-os-linux\libl1.so"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group @"bin\clang-linux-3.9.0\debug\target-os-linux\test.rsp"  
['clang++', '-Wl,-R', '-Wl,C:\\Users\\eldnew8\\AppData\\Local\\Temp\\tmplhdgla\\bin\\clang-linux-3.9.0\\debug\\target-os-linux', '-Wl,-rpath-link', '-Wl,C:\\Users\\eldnew8\\AppData\\Local\\Temp\\tmplhdgla\\bin\\clang-linux-3.9.0\\debug\\target-os-linux', '-o', 'bin\\clang-linux-3.9.0\\debug\\target-os-linux\\test', '-Wl,--start-group', 'bin\\clang-linux-3.9.0\\debug\\target-os-linux\\main.o', 'bin\\clang-linux-3.9.0\\debug\\target-os-linux\\libl1.so', '-Wl,-Bstatic', '-Wl,-Bdynamic', '-Wl,--end-group', '@bin\\clang-linux-3.9.0\\debug\\target-os-linux\\test.rsp']  
...failed clang-linux.link bin\clang-linux-3.9.0\debug\target-os-linux\test...  
...failed updating 1 target...  
...updated 7 targets...  
  
}}}  
STDERR {{{  
  
}}}  
  
*** Copying the state of working dir into 'failed_test' ***  
  
The failed command was:  
b2 -sBOOST_BUILD_PATH="E:\Programming\VersionControl\modular-boost\tools\build\test/.." --user-config= -sPYTHON_CMD=C:\Utilities\python\x64\2.7.16\python.exe target-os=linux -j1 -d2 toolset=clang-linux-3.9.0 --test-config="E:\Programming\VersionControl\modular-boost\tools\build\test\test-config.jam"  
stacktrace {{{  
at line 608 of E:\Programming\VersionControl\modular-boost\tools\build\test\BoostBuild.py (fail_test)  
    from line 497 of E:\Programming\VersionControl\modular-boost\tools\build\test\BoostBuild.py (run_build_system)  
    from line 110 of E:\Programming\VersionControl\modular-boost\tools\build\test\TestToolset.py (test_toolset)  
    from line 19 of toolset_clang_linux.py (<module>)  
  
}}}  
```  
  
I am trying in this PR to add response files to the linking actions in clang-linux.jam file when run on Windows because Windows has a line length limit which is exceeded by certain tests in a few Boost libraries.  
  
Would someone familiar with the Boost Build test system look at this issue and try to fix the test system so that response files in the clang-linux.jam file pass the 'toolset_clang_linux' test please ? I know the Boost Build test system is largely Python with a little C++ thrown in, and I do understand Python, but the design is pretty clever and intricate stuff and it would be nice if the original developer(s) could fix this "bug" in the test system so that users of clang-linux under Windows could run library tests without failing because the command line is too long.  
  
Once the Boost Build test system is fixed this PR will pass all its tests.  
  
Thank you !

---

## Comment 4

> Username: eldiener  
> Created_at: 2020-04-29 19:50:53 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-621426513  

Any objections merging this to 'develop' ?

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2020-04-29 19:53:51 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-621427991  

I'll try and look at fixing the test system tonight. I've been rather busy recently :-(

---

## Comment 6

> Username: eldiener  
> Created_at: 2020-04-29 20:51:41 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-621457242  

No problem, I did not mean to push this issue other than just to get an acknowledgment that it exists. Steve's Python code is fairly complicated and I just did not have the patience to try to follow it and see how this could be fixed. My impression is just that his code did not take into account the idea that a part of a command could be a generated file expansion. Maybe something along the lines of before his code validates a command it needs to expand the command by recursively inserting whatever is in any generated file expansions into the actual command being examined. Then he can use his code as it now is to validate. I just could not figure out where in the Python code he is actually reading in a command line, before validating it.

---

## Comment 7

> Username: grafikrobot  
> Created_at: 2020-05-01 17:16:07 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-622477466  

So.. took a bit of work. But I have something working. Here's the deal...  
  
I did a bunch of changes to the current tests:  
https://github.com/boostorg/build/commit/7d9866256d1ce46abaf8355bb618cdebab9cfe62  
  
With that you would need to apply the response file change generically to all clang-linux variants:  
https://gist.github.com/grafikrobot/5fa43cfeb21a8f6ae80247c434c1cbe3  
  
And to deal with that you also need to change the test for clang-linux:  
https://gist.github.com/grafikrobot/4e13de3fbe42c30db3c16a85b25d8c1e

---

## Comment 8

> Username: Kojoley  
> Created_at: 2020-05-01 20:03:22 UTC  
> Updated_at: 2020-05-01 20:04:07 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-622542823  

I use clang-win/mingw toolset and I did not hit the limit even a single time. Could response files be used only when the limit is actually reached? I really hate to not see passed command line options when a build fails.

---

## Comment 9

> Username: eldiener  
> Created_at: 2020-05-01 21:30:32 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-622576596  

The limit is reached on Windows during certain multiprecision tests. You can use the -d2 option to show the contents of response files.

---

## Comment 10

> Username: Kojoley  
> Created_at: 2020-05-01 21:40:59 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-622579744  

> You can use the -d2 option to show the contents of response files.  
  
Well it is a workaround for a workaround and I cannot control everyones CI to add it when I need it.  
  
Not to mention that response file strategy taxes build time because filesystem access syscalls are terribly slow on Windows.

---

## Comment 11

> Username: eldiener  
> Created_at: 2020-05-01 21:51:03 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-622582543  

>   
>   
> > You can use the -d2 option to show the contents of response files.  
>   
> Well it is a workaround for a workaround and I cannot control everyones CI to add it when I need it.  
>   
> Not to mention that response file strategy taxes build time because filesystem access syscalls are terribly slow on Windows.  
  
The msvc.jam file for vc++ use response files extensively in their actions. It was my choice to use response files for clang-linux.jam just on Windows, where gcc is targeted, but Rene wants it used on all platforms in order to pass the toolset-clang-linux.py test, which he had to update to fix the test. Certain tests on Windows will fail because the command line can run too long, and the response files solve that problem. I do not know what else to say. Is it better to have the tests fail on Winoews because the command line is too long ? I do not think so.

---

## Comment 12

> Username: eldiener  
> Created_at: 2020-05-01 21:52:31 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-622582957  

>   
>   
> > You can use the -d2 option to show the contents of response files.  
>   
> Well it is a workaround for a workaround and I cannot control everyones CI to add it when I need it.  
>   
> Not to mention that response file strategy taxes build time because filesystem access syscalls are terribly slow on Windows.  
  
The clang-win.jam file uses response files also, since it inherits its actions from msvc.jam.

---

## Comment 13

> Username: eldiener  
> Created_at: 2020-05-02 14:19:03 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-622960687  

>   
>   
> So.. took a bit of work. But I have something working. Here's the deal...  
>   
> I did a bunch of changes to the current tests:  
> [7d98662](https://github.com/boostorg/build/commit/7d9866256d1ce46abaf8355bb618cdebab9cfe62)  
>   
> With that you would need to apply the response file change generically to all clang-linux variants:  
> https://gist.github.com/grafikrobot/5fa43cfeb21a8f6ae80247c434c1cbe3  
>   
> And to deal with that you also need to change the test for clang-linux:  
> https://gist.github.com/grafikrobot/4e13de3fbe42c30db3c16a85b25d8c1e  
  
I am still seeing a failure with the toolset_clang_linux.py test when I test it locally on Windows 10 with the files above and the latest 'develop' changes which incorporate your other changes for allowing generated file expansion.

---

## Comment 14

> Username: grafikrobot  
> Created_at: 2020-05-05 00:34:45 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-623780671  

> I am still seeing a failure with the toolset_clang_linux.py test when I test it locally on Windows 10 with the files above and the latest 'develop' changes which incorporate your other changes for allowing generated file expansion.  
  
Can you add the changes to the PR so I can see how CI behaves?

---

## Comment 15

> Username: eldiener  
> Created_at: 2020-05-05 16:05:43 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-624146103  

>   
>   
> > I am still seeing a failure with the toolset_clang_linux.py test when I test it locally on Windows 10 with the files above and the latest 'develop' changes which incorporate your other changes for allowing generated file expansion.  
>   
> Can you add the changes to the PR so I can see how CI behaves?  
  
They should all be there now.

---

## Comment 16

> Username: eldiener  
> Created_at: 2020-05-14 14:37:12 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-628678106  

Is there still an intention to fix the tests so that generated file expansion works with the toolset_clang_linux test ?

---

## Comment 17

> Username: grafikrobot  
> Created_at: 2020-05-14 14:45:51 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-628683337  

> Is there still an intention to fix the tests so that generated file expansion works with the toolset_clang_linux test ?  
  
Yes, sorry... been busy with other things.

---

## Comment 18

> Username: grafikrobot  
> Created_at: 2020-05-15 01:27:42 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-628971847  

Trying again.. Merge the current develop state and use this for clang-linux-3.9.0.py (https://gist.github.com/grafikrobot/4d3dbbf2fb6166221c0c270e6f46caf9)

---

## Comment 19

> Username: Kojoley  
> Created_at: 2020-06-10 13:22:40 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-642005245  

What happened here?

---

## Comment 20

> Username: grafikrobot  
> Created_at: 2020-06-10 21:35:56 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-642280649  

> What happened here?  
  
@Kojoley good question.

---

## Comment 21

> Username: eldiener  
> Created_at: 2020-06-11 03:14:13 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-642379819  

I merged the PR. It is totally valid. If you find something invalid about it please mention it.

---

## Comment 22

> Username: Kojoley  
> Created_at: 2020-06-16 14:33:05 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-644804396  

> I merged the PR. It is totally valid. If you find something invalid about it please mention it.  
  
You made clang-linux use rsp files on Linux for no reason. Also, toolset_clang_linux test now tests nothing, all flags are now in a rsp file and you are not testing it.

---

## Comment 23

> Username: eldiener  
> Created_at: 2020-06-16 15:14:23 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-644829196  

Talk to Renee, not me.

---

## Comment 24

> Username: Kojoley  
> Created_at: 2020-06-16 15:22:00 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-644834253  

Why I should talk to Renee if you authored and self-merged the PR?

---

## Comment 25

> Username: eldiener  
> Created_at: 2020-06-16 15:29:43 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-644839030  

Because Renee is the person who supplied the "fix" for the toolset_clang_linux test and specified that response files should be used on all platforms for clang-linux.jam so that the test would pass. I submitted a perfectly valid PR a long while back, which used response files for clang-linux.jam on Windows because of the limitations of the Windows command line length.

---

## Comment 26

> Username: Kojoley  
> Created_at: 2020-06-16 15:36:30 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-644842677  

But you had merged the PR.  
  
> which used response files for clang-linux.jam on Windows  
  
It uses rsp files on **Linux** after this PR too.

---

## Comment 27

> Username: grafikrobot  
> Created_at: 2020-06-16 15:38:40 UTC  
> Updated_at: 2020-06-16 15:39:37 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-644843871  

I do have some blame on this. As I prefer to not bifurcate the toolset between the platforms. I.e. having hard-wired `if`'s to select `action` variations. Having all platforms use the response file was a way to keep that (per my original consolidation https://gist.github.com/grafikrobot/5fa43cfeb21a8f6ae80247c434c1cbe3) and pass the tests. I was going to ask @eldiener to change the new variation of this that appeared on the latest changes. But didn't get that chance.  
  
PS. My name is "René" not "Renee".

---

## Comment 28

> Username: Kojoley  
> Created_at: 2020-06-16 15:55:44 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-644853556  

I wished the rsp cancer go away, instead now we have it everywhere because someone somehow did not fit a command into thousands of chars (into 8k?).

---

## Comment 29

> Username: eldiener  
> Created_at: 2020-06-16 17:28:19 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-644904468  

I can change the clang-linux.jam, Rene, so that when the link command is executed on Windows it uses response files whereas when the link command is executed in another OS it does not use response files. Will the toolset_clang_linux test pass with that change no matter on what OS it is tested on ?

---

## Comment 30

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:47 UTC  
> Url: https://github.com/boostorg/build/pull/567#issuecomment-932819960  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
