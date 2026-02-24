# #125 - Segmentation Fault on Windows [Closed]

> Username: frenchtoast747  
> Created at: 2016-03-04 00:17:18 UTC  
> Updated at: 2016-03-15 17:39:00 UTC  
> Closed at: 2016-03-15 17:39:00 UTC  
> Url: https://github.com/boostorg/build/issues/125  

On one of our larger projects, building the entire project all at once can produce a segmentation fault. To try and pinpoint the problem I broke out Dr. Memory.  
  
~~There were several uninitialized read errors for the `builtin_calc` rule due to using the `sign` variable without it being initialized. Changing the declaration for `sign` to~~  
  
```  
char const * sign = "";  
```  
  
~~removed all of the uninitialized read errors.~~  
  
There are many LEAK errors and I believe these to be the cause of the seg fault. The majority of the errors are `LEAK 0 direct bytes` which a few `LEAK 8 direct bytes`.  
  
[The Dr. Memory output file can be found here.](https://github.com/boostorg/build/files/157906/results.txt)

---

## Comment 1

> Username: swatanabe  
> Created at: 2016-03-04 00:28:51 UTC  
> Url: https://github.com/boostorg/build/issues/125#issuecomment-192035037  

AMDG  
  
On 03/03/2016 05:17 PM, Aaron Boman wrote:  
  
> On one of our larger projects, building the entire project all at once can produce a segmentation fault. To try and pinpoint the problem I broke out Dr. Memory.  
>   
> There were several uninitialized read errors for the `builtin_calc` rule due to using the `sign` variable without it being initialized. Changing the declaration for `sign` to  
>   
> ```  
> char const * sign = "";  
> ```  
>   
> removed all of the uninitialized read errors.  
  
  I don't see the variable anywhere in  
builtin_calc, and git blame says the  
more recent change to it was in 2012.  
What version are you using, exactly?  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: frenchtoast747  
> Created at: 2016-03-04 03:26:01 UTC  
> Url: https://github.com/boostorg/build/issues/125#issuecomment-192080537  

My apologies, that must have been a local repo change. I didn't think anyone would touch the engine itself.

---

## Comment 3

> Username: frenchtoast747  
> Created at: 2016-03-04 03:28:09 UTC  
> Url: https://github.com/boostorg/build/issues/125#issuecomment-192081079  

While watching the build, I noticed that the RAM usage peaked to around 2GB before crashing. I'm assuming that some call to `malloc()` is failing to get enough memory and is then returning `NULL`. Later on, that null pointer is being dereferenced producing the seg fault. I don't know if this is even worth looking at.

---

## Comment 4

> Username: swatanabe  
> Created at: 2016-03-04 04:42:37 UTC  
> Url: https://github.com/boostorg/build/issues/125#issuecomment-192099984  

AMDG  
  
On 03/03/2016 08:28 PM, Aaron Boman wrote:  
  
> While watching the build, I noticed that the RAM usage peaked to around 2GB before crashing. I'm assuming that some call to `malloc()` is failing to get enough memory and is then returning `NULL`. Later on, that null pointer is being dereferenced producing the seg fault. I don't know if this is even worth looking at.  
  
It would be nice to have a better error.  
I also have some ideas on how to save  
memory--2GB is kind of ridiculous.  
  
The main culprits for memory usage are:  
strings: ~20 %  
TARGET: >10 % for all TARGETs without even  
    counting dynamic memory owned by the TARGET.  
file_info: ~5%  
single element lists: ~5%  
The global modules table: ~5%  
  
Note: I got these numbers from running in  
$BOOST_ROOT/status, with -d0 -d+10 and a  
few custom printfs.  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: frenchtoast747  
> Created at: 2016-03-04 14:30:07 UTC  
> Url: https://github.com/boostorg/build/issues/125#issuecomment-192302718  

I'm still working on trying to reproduce the seg fault in a smaller project.  
  
Slightly more info, the seg fault occurs faster with the debug version of Boost.Build over the release version (the RAM usage skyrockets faster) and never gets to the updating actions which makes sense as I would expect the debug version to use more memory faster.  
  
In the release version, updating actions are being performed and it appears to be working correctly. 100s of updating actions are performed before the seg fault occurs (just as the RAM usage peaks at 2GB).  
  
I assume the rescanning and compiling/execution of actions are part of the problem here as well. Would the rescanning consume that much more memory?  
  
There are a total of 37,578 targets found before building.

---

## Comment 6

> Username: swatanabe  
> Created at: 2016-03-04 15:31:52 UTC  
> Url: https://github.com/boostorg/build/issues/125#issuecomment-192324618  

AMDG  
  
On 03/04/2016 07:30 AM, Aaron Boman wrote:  
  
> I'm still working on trying to reproduce the seg fault in a smaller project.  
>   
> Slightly more info, the seg fault occurs faster with the debug version of Boost.Build over the release version (the RAM usage skyrockets faster) and never gets to the updating actions which makes sense as I would expect the debug version to use more memory faster.  
>   
> In the release version, updating actions are being performed and it appears to be working correctly. 100s of updating actions are performed before the seg fault occurs (just as the RAM usage peaks at 2GB).  
>   
> I assume the rescanning and compiling/execution of actions are part of the problem here as well. Would the rescanning consume that much more memory?  
  
  You'd have to be rescanning a lot  
of targets before it became a problem.  
Executing the actions could be an  
issue if they produce a lot of output,  
since the output is buffered.  Still,  
all the memory used in executing  
actions should be released when the  
action finishes, unless there's a leak  
somewhere.  
  
> There are a total of 37,578 targets found before building.  
  
In Christ,  
Steven Watanabe

---

## Comment 7

> Username: swatanabe  
> Created at: 2016-03-04 17:16:45 UTC  
> Url: https://github.com/boostorg/build/issues/125#issuecomment-192364395  

AMDG  
  
  I'm seeing lots of memory errors when I activate  
BJAM_NO_MEM_CACHE.  (Also, someone seems to have  
thought that without BJAM_NO_MEM_CACHE it's okay  
to leak memory).  Let me clean that up, and then  
we'll see where we're at.  
  
In Christ,  
Steven Watanabe

---

## Comment 8

> Username: swatanabe  
> Created at: 2016-03-04 19:37:28 UTC  
> Url: https://github.com/boostorg/build/issues/125#issuecomment-192431806  

AMDG  
  
  I just fixed a major memory leak.  Total  
memory usage dropped from about 570 MB to  
500 MB in my test.  valgrind gives a clean  
report for the current develop branch, now.  
  
In Christ,  
Steven Watanabe

---

## Comment 9

> Username: frenchtoast747  
> Created at: 2016-03-05 00:01:36 UTC  
> Url: https://github.com/boostorg/build/issues/125#issuecomment-192530720  

Alright, I tried it out.  
  
Previously, at the time that the actions started to run, the RAM usage was sitting about 1.7GB and it would climb about 1MB every 5-10s.  
  
Now, it starts the updating actions around 1.9GB RAM, but only climbs a few KB or so every 5-10s. So the leak seems to have been plugged.  
  
However, I still got a seg fault :(  
  
I'm going to still try and recreate the problem in a throw-away project, but it seems that memory optimization is the only solution at this point.

---

## Comment 10

> Username: swatanabe  
> Created at: 2016-03-05 18:07:56 UTC  
> Url: https://github.com/boostorg/build/issues/125#issuecomment-192699695  

AMDG  
  
On 03/04/2016 05:01 PM, Aaron Boman wrote:  
  
> Alright, I tried it out.  
>   
> Previously, at the time that the actions started to run, the RAM usage was sitting about 1.7GB and it would climb about 1MB every 5-10s.  
>   
> Now, it starts the updating actions around 1.9GB RAM, but only climbs a few KB or so every 5-10s. So the leak seems to have been plugged.  
  
  I'm a little surprised that the base memory usage  
increased.  In a release build, the changes I made  
should only add some frees and also delay a few  
(small constant per file loaded) frees that  
happened to early.  
  
> However, I still got a seg fault :(  
>   
> I'm going to still try and recreate the problem in a throw-away project, but it seems that memory optimization is the only solution at this point.  
  
hash.c:239:  
  hp->items.more = i ? 2 \* hp->items.nel : hp->inel;  
  
  Try removing the "2 *".  This will reduce the  
growth rate of hash so it wastes less memory.  
  
In Christ,  
Steven Watanabe

---

## Comment 11

> Username: frenchtoast747  
> Created at: 2016-03-07 14:12:55 UTC  
> Url: https://github.com/boostorg/build/issues/125#issuecomment-193262341  

Removed the "2*". Memory growth seemed to be just about the same. Still got the seg fault.  
  
This time, all of the CreateProcessA() calls just before the seg fault failed with the [1450 error code](https://msdn.microsoft.com/en-us/library/windows/desktop/ms681385%28v=vs.85%29.aspx#error_no_system_resources).  
  
It seems we've hit the threshold for maximum number of targets allowed on the 32bit build on Windows.  
  
I think we have a workaround figured out by building all targets in multiple passes as the majority of the targets being built are `unit-test` runs and are easy to split up in batches.

---

## Comment 12

> Username: swatanabe  
> Created at: 2016-03-07 18:42:33 UTC  
> Url: https://github.com/boostorg/build/issues/125#issuecomment-193388217  

AMDG  
  
On 03/04/2016 07:30 AM, Aaron Boman wrote:  
  
> There are a total of 37,578 targets found before building.  
  
  I'm quite surprised by that.  37,578 is  
not that many.  
$ cd $BOOST_ROOT/status  
$ b2  
<snip>  
...patience... [ \* 20 ]  
...found 172065 targets...  
...updating 79672 targets...  
  
In Christ,  
Steven Watanabe

---

## Comment 13

> Username: frenchtoast747  
> Created at: 2016-03-07 21:08:33 UTC  
> Url: https://github.com/boostorg/build/issues/125#issuecomment-193449475  

Finally got the actual failure point:  
  
```  
b2.exe!targetentry(_targets * chain=0x00000000, _target * target=0x77c3f550) Line 261  
b2.exe!builtin_depends(frame * frame=0x001ab710, int flags=1) Line 593  
b2.exe!function_run(_function * function_=0x0052cd58, frame * frame=0x001ab710, _stack * s=0x01236a88) Line 3773  
b2.exe!evaluate_rule(_rule * rule=0x0052c9f4, _object * rulename=0x0052cd0c, frame * frame=0x001ab710) Line 150  
b2.exe!function_call_rule(_jam_function * function=0x00c55dd8, frame * frame=0x001abc14, _stack * s=0x01236a88, int n_args=2, const char * unexpanded=0x0052cd0c, _object * file=0x00c4053c, int line=61) Line 479  
b2.exe!function_run(_function * function_=0x00c55dd8, frame * frame=0x001abc14, _stack * s=0x01236a88) Line 4337  
b2.exe!evaluate_rule(_rule * rule=0x00c40f94, _object * rulename=0x00c8aa84, frame * frame=0x001abc14) Line 150  
b2.exe!function_call_member_rule(_jam_function * function=0x00b404a0, frame * frame=0x001ac18c, _stack * s=0x01236a88, int n_args=3, _object * rulename=0x0053b35c, _object * file=0x00b8e004, int line=156) Line 578  
b2.exe!function_run(_function * function_=0x00b404a0, frame * frame=0x001ac18c, _stack * s=0x01236a88) Line 4346  
b2.exe!evaluate_rule(_rule * rule=0x0052f74c, _object * rulename=0x00450f9c, frame * frame=0x001ac18c) Line 150  
b2.exe!headers(_target * t=0x6eb2a224) Line 103  
b2.exe!make0(_target * t=0x6eb2a224, _target * p=0x1a1ea608, int depth=16, COUNTS * counts=0x001ad034, int anyhow=1, _target * rescanning=0x00000000) Line 367  
b2.exe!make0(_target * t=0x7585f398, _target * p=0x1a1ea608, int depth=15, COUNTS * counts=0x001ad034, int anyhow=1, _target * rescanning=0x00000000) Line 412  
b2.exe!make0(_target * t=0x6eb2813c, _target * p=0x1a1ea608, int depth=14, COUNTS * counts=0x001ad034, int anyhow=1, _target * rescanning=0x00000000) Line 434  
b2.exe!make0(_target * t=0x3925f2e0, _target * p=0x1a1ea608, int depth=13, COUNTS * counts=0x001ad034, int anyhow=1, _target * rescanning=0x00000000) Line 412  
b2.exe!make0(_target * t=0x6eb27788, _target * p=0x1a1ea608, int depth=12, COUNTS * counts=0x001ad034, int anyhow=1, _target * rescanning=0x00000000) Line 434  
b2.exe!make0(_target * t=0x2288a400, _target * p=0x1a1ea608, int depth=11, COUNTS * counts=0x001ad034, int anyhow=1, _target * rescanning=0x00000000) Line 412  
b2.exe!make0(_target * t=0x6eb04d30, _target * p=0x1a1ea608, int depth=10, COUNTS * counts=0x001ad034, int anyhow=1, _target * rescanning=0x00000000) Line 434  
b2.exe!make0(_target * t=0x7ef2c030, _target * p=0x1a1ea608, int depth=9, COUNTS * counts=0x001ad034, int anyhow=1, _target * rescanning=0x00000000) Line 412  
b2.exe!make0(_target * t=0x6eb020e4, _target * p=0x1a1ea608, int depth=8, COUNTS * counts=0x001ad034, int anyhow=1, _target * rescanning=0x00000000) Line 434  
b2.exe!make0(_target * t=0x7ef24150, _target * p=0x1a1ea608, int depth=7, COUNTS * counts=0x001ad034, int anyhow=1, _target * rescanning=0x00000000) Line 412  
b2.exe!make0(_target * t=0x1a1ea6e0, _target * p=0x1a1ea608, int depth=6, COUNTS * counts=0x001ad034, int anyhow=1, _target * rescanning=0x00000000) Line 434  
b2.exe!make0(_target * t=0x1a1ea608, _target * p=0x1a1ea530, int depth=5, COUNTS * counts=0x001ad034, int anyhow=1, _target * rescanning=0x00000000) Line 412  
b2.exe!make0(_target * t=0x1a1ea530, _target * p=0x1a1ea458, int depth=4, COUNTS * counts=0x001ad034, int anyhow=1, _target * rescanning=0x00000000) Line 412  
b2.exe!make0(_target * t=0x1a1ea458, _target * p=0x1a1ea314, int depth=3, COUNTS * counts=0x001ad034, int anyhow=1, _target * rescanning=0x00000000) Line 412  
b2.exe!make0(_target * t=0x1a1ea314, _target * p=0x1a1ea1d0, int depth=2, COUNTS * counts=0x001ad034, int anyhow=1, _target * rescanning=0x00000000) Line 412  
b2.exe!make0(_target * t=0x1a1ea1d0, _target * p=0x22456db8, int depth=1, COUNTS * counts=0x001ad034, int anyhow=1, _target * rescanning=0x00000000) Line 412  
b2.exe!make0(_target * t=0x22456db8, _target * p=0x00000000, int depth=0, COUNTS * counts=0x001ad034, int anyhow=1, _target * rescanning=0x00000000) Line 412  
b2.exe!make(_list * targets=0x2582b768, int anyhow=1) Line 115  
b2.exe!builtin_update_now(frame * frame=0x001ad500, int flags=0) Line 1523  
b2.exe!function_run(_function * function_=0x0052e120, frame * frame=0x001ad500, _stack * s=0x01236a88) Line 3773  
b2.exe!evaluate_rule(_rule * rule=0x0052d70c, _object * rulename=0x0052e0d4, frame * frame=0x001ad500) Line 150  
b2.exe!function_call_rule(_jam_function * function=0x00600ae8, frame * frame=0x001add0c, _stack * s=0x01236a88, int n_args=1, const char * unexpanded=0x0052e0d4, _object * file=0x0054b58c, int line=973) Line 479  
b2.exe!function_run(_function * function_=0x00600ae8, frame * frame=0x001add0c, _stack * s=0x01236a88) Line 4337  
b2.exe!parse_file(_object * f=0x0054b58c, frame * frame=0x001add0c) Line 57  
b2.exe!function_run(_function * function_=0x005958a8, frame * frame=0x001add0c, _stack * s=0x01236a88) Line 4550  
b2.exe!evaluate_rule(_rule * rule=0x005b7a24, _object * rulename=0x00559134, frame * frame=0x001add0c) Line 150  
b2.exe!function_call_rule(_jam_function * function=0x0056c900, frame * frame=0x001ae200, _stack * s=0x01236a88, int n_args=3, const char * unexpanded=0x00559134, _object * file=0x0057be8c, int line=289) Line 479  
b2.exe!function_run(_function * function_=0x0056c900, frame * frame=0x001ae200, _stack * s=0x01236a88) Line 4337  
b2.exe!evaluate_rule(_rule * rule=0x0052f6bc, _object * rulename=0x0058083c, frame * frame=0x001ae200) Line 150  
b2.exe!function_call_rule(_jam_function * function=0x005d8778, frame * frame=0x001aea0c, _stack * s=0x01236a88, int n_args=1, const char * unexpanded=0x0058083c, _object * file=0x0058fcec, int line=139) Line 479  
b2.exe!function_run(_function * function_=0x005d8778, frame * frame=0x001aea0c, _stack * s=0x01236a88) Line 4337  
b2.exe!parse_file(_object * f=0x0058fcec, frame * frame=0x001aea0c) Line 57  
b2.exe!function_run(_function * function_=0x005f8fc0, frame * frame=0x001aea0c, _stack * s=0x01236a88) Line 4550  
b2.exe!evaluate_rule(_rule * rule=0x0052f65c, _object * rulename=0x005394c4, frame * frame=0x001aea0c) Line 150  
b2.exe!function_call_rule(_jam_function * function=0x005e0b80, frame * frame=0x001af964, _stack * s=0x01236a88, int n_args=1, const char * unexpanded=0x005394c4, _object * file=0x00546c64, int line=8) Line 479  
b2.exe!function_run(_function * function_=0x005e0b80, frame * frame=0x001af964, _stack * s=0x01236a88) Line 4337  
b2.exe!parse_file(_object * f=0x00546c64, frame * frame=0x001af964) Line 57  
b2.exe!function_run(_function * function_=0x005fdda0, frame * frame=0x001af964, _stack * s=0x01236a88) Line 4550  
b2.exe!parse_file(_object * f=0x0051ed84, frame * frame=0x001af964) Line 57  
b2.exe!main(int argc=6, char * * argv=0x0051fe7c, char * * arg_environ=0x0051ce58) Line 536  
b2.exe!__tmainCRTStartup() Line 255  
b2.exe!mainCRTStartup() Line 165  
kernel32.dll!@BaseThreadInitThunk@12()  
ntdll.dll!___RtlUserThreadStart@8()  
ntdll.dll!__RtlUserThreadStart@8()  
```  
  
``` C  
TARGETS * targetentry( TARGETS * chain, TARGET * target )  
{  
    TARGETS * const c = (TARGETS *)BJAM_MALLOC( sizeof( TARGETS ) );  
--> c->target = target;  
  
    if ( !chain ) chain = c;  
    else chain->tail->next = c;  
    chain->tail = c;  
    c->next = 0;  
  
    return chain;  
}  
```  
  
And it's as I suspected. BJAM_MALLOC fails and then tries to de-reference an attribute.  
  
Looking at the traceback alone is there anything that stands out?

---

## Comment 14

> Username: swatanabe  
> Created at: 2016-03-07 21:50:06 UTC  
> Url: https://github.com/boostorg/build/issues/125#issuecomment-193469568  

AMDG  
  
On 03/07/2016 02:08 PM, Aaron Boman wrote:  
  
> Finally got the actual failure point:  
> <snip>  
>   
> ``` C  
> TARGETS * targetentry( TARGETS * chain, TARGET * target )  
> {  
>     TARGETS * const c = (TARGETS *)BJAM_MALLOC( sizeof( TARGETS ) );  
> --> c->target = target;  
>   
>     if ( !chain ) chain = c;  
>     else chain->tail->next = c;  
>     chain->tail = c;  
>     c->next = 0;  
>   
>     return chain;  
> }  
> ```  
>   
> And it's as I suspected. BJAM_MALLOC fails and then tries to de-reference an attribute.  
>   
> Looking at the traceback alone is there anything that stands out?  
  
  Only that if this particular struct accounts  
for a significant fraction of memory usage,  
the memory it uses can be cut in half.  
Memory usage is approximately  
  
# of calls to targetentry \* 4 \* sizeof( void \* )  
  
(Including 1 pointer worth of overhead.)  
  
In a much smaller test I see:  
  
->05.23% (2,615,568B) 0x41EC72: targetlist (rules.c:260)  
  
which isn't much compared to  
->29.16% (14,580,152B) 0x41AC40: string_set_insert.constprop.1  
(object.c:114)  
->20.07% (10,038,248B) 0x4123B6: hash_insert (hash.c:119)  
  
In Christ,  
Steven Watanabe

---

## Comment 15

> Username: frenchtoast747  
> Created at: 2016-03-15 17:39:00 UTC  
> Url: https://github.com/boostorg/build/issues/125#issuecomment-196941973  

I'm going to go ahead and close this issue (at least for now) since we have a workaround and this issue is heavily contingent upon reproducing the problem with a temp project (which probably won't happen soon).  
  
Thanks @swatanabe for looking into this and fixing the memory leaks that existed!
