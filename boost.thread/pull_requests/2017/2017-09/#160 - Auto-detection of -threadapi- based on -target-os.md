# #160 Auto-detection of <threadapi> based on <target-os> [Merged]

> Username: karzhenkov  
> Created at: 2017-09-22 06:24:38 UTC  
> Updated at: 2017-10-04 18:28:13 UTC  
> Merged at: 2017-09-28 21:50:39 UTC  
> Closed at: 2017-09-28 21:50:39 UTC  
> Url: https://github.com/boostorg/thread/pull/160  

Currently Boost.Thread selects the default `<threadapi>` value based on host system name. How to make it dependent of `<target-os>` was discussed in tickets [#3393](https://svn.boost.org/trac10/ticket/3393) and [#7706](https://svn.boost.org/trac10/ticket/7706). My solution is somewhat similar to [this proposal](https://svn.boost.org/trac10/attachment/ticket/3393/threadapi2.diff) but also allows `<threadapi>` to be specified in build request and improves the `<tag>` feature behavior:  
  
* `<threadapi>` has an additional value `native` which is the default.  
  If build request doesn't specify other value, it is replaced  
  either with "pthread" or "win32" depending on `<target-os>`.  
* `<tag>` modifies name of generated library only if resulting `<threadapi>`  
  value differs from one that would have been chosen to replace `native`  
  according to `<target-os>.`

---

## Comment 1

> Username: viboes  
> Created_at: 2017-09-22 06:47:24 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-331364876  

Hi,  
  
I'm not an expert on Bjam.  
  
AFAIK, the default is already the native, isn't it?

---

## Comment 2

> Username: karzhenkov  
> Created_at: 2017-09-22 07:09:23 UTC  
> Updated_at: 2017-09-22 07:11:15 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-331368928  

Currently the default value for `<threadapi>` is selected as follows:  
>`local api = pthread ;`  
`if [ os.name ] = "NT" { api = win32 ; }`  
  
Here `os.name` is name of the host system. The problem arises when target system differs from the host.  
  
I'm trying to cross-compile for QNX and my native API is `pthread`. But I do this on Windows, and API defaults to `win32`.  
  
Even if I explicitly specify `threadapi` in the build request, there is an issue with the `tag`. My library gets named `libboost_thread_pthread.so` instead of expected `libboost_thread.so`.

---

## Comment 3

> Username: viboes  
> Created_at: 2017-09-23 04:24:03 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-331609645  

I don't understand how that tag is used. Could you help me? What should be the name of the library depending on <threadapi> <host-os> and <target-os>?

---

## Comment 4

> Username: viboes  
> Created_at: 2017-09-23 04:56:33 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-331610808  

Hi,  
  
  
in https://ci.appveyor.com/project/viboes/thread/build/1.0.57-develop we see some issues with `native_handle`. We don't have `native_handle` in win32.  
  
```   
libs\thread\test\sync\mutual_exclusion\recursive_timed_mutex\native_handle_pass.cpp(32) : fatal error C1189: #error :  "Test not applicable: BOOST_THREAD_DEFINES_RECURSIVE_TIMED_MUTEX_NATIVE_HANDLE not defined for this platform as not supported"  
    call "C:\Users\appveyor\AppData\Local\Temp\1\b2_msvc_12.0_vcvarsall_x86.cmd" >nul  
cl /Zm800 -nologo @"bin.v2\libs\thread\test\rec_timed_mutex_native_handle_p.test\msvc-12.0\debug\threading-multi\sync\mutual_exclusion\recursive_timed_mutex\native_handle_pass.obj.rsp"   
...failed compile-c-c++ bin.v2\libs\thread\test\rec_timed_mutex_native_handle_p.test\msvc-12.0\debug\threading-multi\sync\mutual_exclusion\recursive_timed_mutex\native_handle_pass.obj...  
```

---

## Comment 5

> Username: karzhenkov  
> Created_at: 2017-09-23 06:47:29 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-331615271  

When Boost.Thread is compiled with non-native `<threadapi>`, it marks the created library with the corresponding suffix `_win32` or `_pthread` added to the name. This is implemented using the `<tag>` feature.  
  
For Windows it's possible to use both options, and we'll get the names `libboost_thread.dll` (native variant, without `<threadapi>` suffix) and `libboost_thread_pthread.dll`.  
  
On other platforms native variant (and perhaps the only one) is `pthread`, so the name must be `libboost_thread.so`. Name `libboost_thread_pthread.so` is incorrect; name `libboost_thread_win32.so` is probably not applicable.  
  
Boost.Build can also add its own suffixes not considered here.

---

## Comment 6

> Username: viboes  
> Created_at: 2017-09-23 07:08:07 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-331616173  

Thanks for the explanation about the tag.  
  
Any comment about the regression for native_handle?

---

## Comment 7

> Username: viboes  
> Created_at: 2017-09-23 07:26:22 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-331617010  

The native_handle test run as follows  
https://github.com/boostorg/thread/blob/develop/test/Jamfile.v2#L322  
  
```  
 [ thread-run2-noit-pthread ./sync/conditions/condition_variable/native_handle_pass.cpp : condition_variable__native_handle_p ]  
```  
  
where thread-run2-noit-pthread is defined as https://github.com/boostorg/thread/blob/develop/test/Jamfile.v2#L163  
  
```  
rule thread-run2-noit-pthread ( sources : name )  
{  
    sources = $(sources) winrt_init.cpp ;  
    return  
    [ run $(sources) ../build//boost_thread : : : <threadapi>win32:<build>no  
      : $(name) ]  
    [ run $(sources) ../src/tss_null.cpp ../build//boost_thread/<link>static  
        : : : <threadapi>win32:<build>no  
      : $(name)_lib ]  
    #[ run $(sources) ../build//boost_thread : : :  
    #  <define>BOOST_THREAD_DONT_PROVIDE_INTERRUPTIONS  
    #  : $(name)_noit ]  
    ;  
}  
```  
  
It is like with your changed `<threadapi>win32` was not defined.  
  
I suspect that the default `<threadapi>native` has something to be with this.  
  
Do you know how to fix this?

---

## Comment 8

> Username: karzhenkov  
> Created_at: 2017-09-23 10:41:19 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-331625846  

I tried to change the `usage-requirements`, but now I can't build the library. The reason is not clear yet.

---

## Comment 9

> Username: karzhenkov  
> Created_at: 2017-09-23 17:25:27 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-331654869  

Regression (I hope) is fixed. Perhaps there is a more elegant solution, but I just added the same detection logic into "test/Jamfile.v2".

---

## Comment 10

> Username: viboes  
> Created_at: 2017-09-24 04:51:31 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-331687073  

Yes. This was the route cause and it fixes the issue for Boost.Thread tests.  
  
We need however to add it on the usage-requirement, otherwise any other library that depends on Boost.Thread will have the same issue, isn't it?

---

## Comment 11

> Username: karzhenkov  
> Created_at: 2017-09-24 16:58:01 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-331723528  

You're right. But I'm not an expert on Bjam too:)  
It turned out to be a bit more complicated. I will think.

---

## Comment 12

> Username: viboes  
> Created_at: 2017-09-24 19:05:26 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-331731875  

Would you mind to check with the Boost.Build experts what can be done here?  
Steven Watanabe know all we could do with bjam.

---

## Comment 13

> Username: karzhenkov  
> Created_at: 2017-09-25 16:39:18 UTC  
> Updated_at: 2017-09-25 16:40:11 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-331939817  

I would be grateful for expert advice:)  
Could Steven join the discussion?  
  
So, we have:  
  
1. Boost.Thread defines non-free feature `<threadapi>` and main `target boost_thread`. When `boost_thread` is built without specifying value of `<threadapi>` in the build request, the default value is used. It seems to be reasonable to make the default value dependent on `<target-os>`.  
This was intent of my revision [2fb41ed](https://github.com/boostorg/thread/pull/160/commits/2fb41edf1adea22c61d78f42f4ec6b7f6c87e025#diff-f7ff2f2ced58a7096ff8d20a8b12938b).  
I introduced additional value for `<threadapi>` (`native`) which became the default to represent "no value" (i. e. "no user request"). Using `<conditional>` in project requirements `native` is replaced with other value (either `win32` or `pthread`) depending on `<target-os>`.  
2. It is also desirable to have actual `<threadapi>` value (deduced by Boost.Thread or provided by user) in the property set used when any target dependent on `boost_thread` is built. It is even desirable to have it when Bjam is selecting the targets to build. For example, in libs/thread/test/Jamfile some targets are required not to build for `<threadapi>win32`.  
The attempt was made in my revision [6ee1c88](https://github.com/boostorg/thread/pull/160/commits/6ee1c888696d65dc2e2c500b3570e6cf40d808af#diff-f7ff2f2ced58a7096ff8d20a8b12938b).  
I added `<conditional>` to usage requirements but used wrong syntax and perhaps wrong approach.  Usage requirements have specific traits:  
- they are added to property set of a target only after all its dependencies are built and therefore cannot affect target selection;  
- they don't replace properties of the target but are added to the property set (even for non-free features).  
  
I'm not sure but the latter, probably, causes target to be build with each of feature values separately (for non-free features).  
  
The questions:  
- Is there any way to achieve the same effect as in [2fb41ed](https://github.com/boostorg/thread/pull/160/commits/2fb41edf1adea22c61d78f42f4ec6b7f6c87e025#diff-f7ff2f2ced58a7096ff8d20a8b12938b) without introducing artificial `<threadapi>` value just to indicate "no value"?  
- How to make feature value deduced by some target available in a project that uses this target as dependency?  
  
Please correct me if I'm mistaken somewhere (especially in describing the problem).

---

## Comment 14

> Username: viboes  
> Created_at: 2017-09-25 17:17:41 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-331950361  

I've send a mail to the Boost.Build ML (boost-build@lists.boost.org)

---

## Review 15 [Commented]

> Username: viboes  
> Created_at: 2017-09-28 01:00:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/160#pullrequestreview-65728288  

📁 build/Jamfile.v2

```diff
 153 |- 
 154 |- feature.feature threadapi : native pthread win32 : propagated ;
 147 |+ feature.feature threadapi : pthread win32 : optional propagated ;
```

> Username: viboes  
> Created_at: 2017-09-28 01:00:08 UTC  
> Updated_at: 2017-09-28 15:43:31 UTC  
> Url: https://github.com/boostorg/thread/pull/160#discussion_r141504289  

I don't know too much of bjam, but shouldn't this go to the threadapi.jam file?


---

## Review 16 [Commented]

> Username: viboes  
> Created_at: 2017-09-28 01:00:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/160#pullrequestreview-65728462  

📁 test/Jamfile.v2

```diff
  20 | import testing ;
  21 |- import property-set ;
  21 |+ import threadapi ;
```

> Username: viboes  
> Created_at: 2017-09-28 01:00:44 UTC  
> Updated_at: 2017-09-28 15:43:31 UTC  
> Url: https://github.com/boostorg/thread/pull/160#discussion_r141504448  

Does it mean that now any Boost library that needs Boost.Thread needs to import threadapi.jam?


---

## Review 17 [Commented]

> Username: viboes  
> Created_at: 2017-09-28 01:01:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/160#pullrequestreview-65728568  

📁 test/Jamfile.v2

```diff
 109 | 
 110 |-         <conditional>@detect_threadapi
 110 |+         <conditional>@threadapi.detect
```

> Username: viboes  
> Created_at: 2017-09-28 01:01:09 UTC  
> Updated_at: 2017-09-28 15:43:31 UTC  
> Url: https://github.com/boostorg/thread/pull/160#discussion_r141504552  

.... and declare this conditional?


---

## Comment 18

> Username: viboes  
> Created_at: 2017-09-28 01:05:19 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-332697736  

This looks quite good.  
  
Andrey, what do you think about this PR?

---

## Comment 19

> Username: karzhenkov  
> Created_at: 2017-09-28 15:58:47 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-332882580  

Hi,  
  
`<conditional>@threadapi.detect` and `import threadapi` are needed only in projects that use actual `<threadapi>` value in the build process. You can take a look at this [my exercise](https://github.com/karzhenkov/bjam-test), subproject "my-thread-app-no-import". Note that for demo purposes `<variant>` (instead of `<thread-os>`) value is used  to deduce default `<threadapi>`.  
  
Definition of `<threadapi>` is moved now to "threadapi.jam". This should work and looks even better.

---

## Comment 20

> Username: viboes  
> Created_at: 2017-09-28 16:44:12 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-332895262  

Ok, so we need to add some documentation for the projects that would use threadapi.  
Would you mind to add this documentation. I believe some Jamfile examples would help to the users.  
  
If you don't know how to do it, you can post the text here.   
I believe that a new file cross_compiling.qbk included from overview.qbk would be good.   
  
I don't see anything about the libraries that can be built depending on the parameters. It will be a good occasion to add it.

---

## Comment 21

> Username: Lastique  
> Created_at: 2017-09-28 18:23:32 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-332922531  

> Andrey, what do you think about this PR?  
  
I'm not a Boost.Build expert, but the intention seems valid. I'm not sure I understood it though - will this change require modification of users' projects? There are a few dependent libraries in Boost and probably quite a few Boost.Build-based projects out there as well. It would be preferable if those continued to compile as they are.

---

## Comment 22

> Username: viboes  
> Created_at: 2017-09-28 19:14:42 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-332936296  

The default has changed. Instead of been derived from the os.name it is derived from the <target-os>. The <threadapi> deduced with the default <target-os> is the same as the one deduced previously with os.name so I don't believe there will be any regression.  
  
Now if the user set the target-os he will obtain the good library.

---

## Comment 23

> Username: viboes  
> Created_at: 2017-09-28 21:50:27 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-332973867  

I've decided to merge it and see how it behaves on regression tests.  
  
We could fix it later if there is some issue or rollback this change.

---

## Comment 24

> Username: viboes  
> Created_at: 2017-09-28 21:51:32 UTC  
> Updated_at: 2017-09-28 21:52:01 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-332974111  

@karzhenkov Thanks you very much for working on this feature/fix.

---

## Comment 25

> Username: Lastique  
> Created_at: 2017-09-28 21:53:57 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-332974680  

But then `test/Jamfile.v2` wouldn't need to be modified, would it? I'm worried that the fact that it required changing means that the dependent projects will have to be modified as well. What's more alarming is that the change essentially is to perform the same kind of auto-detection that the library does, which arguably will be difficult to reproduce downstream. I think, the auto-detection should result in Boost.Thread usage requirement, which should be picked up by all downstream projects, including tests.

---

## Comment 26

> Username: viboes  
> Created_at: 2017-09-28 22:00:15 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-332975959  

I believe that it needed changes because the intermediary change defaulted threadapi to native.  
  
I believe that   
  
https://github.com/boostorg/thread/blob/develop/test/Jamfile.v2#L21  
  
and   
  
https://github.com/boostorg/thread/blob/develop/test/Jamfile.v2#L110  
  
shouldn't be needed now.  
  
@karzhenkov could you confirm?

---

## Comment 27

> Username: viboes  
> Created_at: 2017-09-28 22:22:28 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-332980164  

I've tried commenting these lines and everything is ok for the moment. The test takes some time.

---

## Comment 28

> Username: karzhenkov  
> Created_at: 2017-09-29 19:40:22 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-333220038  

I think, usage requirements is unsuitable instrument here. They are intended for free features only ([Boost.Build ML](https://lists.boost.org/boost-build/2006/04/13561.php)). And they are added to the property set after selecting target alternatives ([The Build Process](http://www.boost.org/build/doc/html/bbv2/overview/build_process.html)), This can be important.  
  
A project that needs to know the underlaying threading API when building  
must contain `<conditional>@threadapi.detect`. Otherwise `<threadapi>` value will be present in the property set only if it is expicitly specified by user in the build request. For this reason, if you delete `<conditional>@threadapi.detect` from the `test/Jamfile.v2`, then the error that was discussed above will again occur.  
  
Nevertheless, I believe that the impact of this PR on other projects will not be critical:  
- In Boost there is no library that uses `<theadapi>` value, except for Boost.Thread.  
- A project that uses specific functionality of underlying threading API is probably not portable. If so, it can contain hardcoded `<threadapi>` in requirements, or failure might be acceptable when building for alien platform.

---

## Comment 29

> Username: viboes  
> Created_at: 2017-09-30 12:22:47 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-333305106  

Hi, well the test seems to say that we need to uncomment :(  
  
https://ci.appveyor.com/project/viboes/thread/build/1.0.108-develop  
(there are other unrelated error, sorry)  
https://ci.appveyor.com/project/viboes/thread/build/1.0.111-develop  
  
As you said , the native_handle error are there :(  
  
This will mean that we have potentially break some users that use <threadapi>.  
Fortunately there is no Boost library using it.  
  
I believe the number of concerned user should be small enough compared to the number of users that could benefit from this feature.

---

## Comment 30

> Username: viboes  
> Created_at: 2017-09-30 12:23:32 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-333305142  

@karzhenkov We need to advertise this on the documentation. Have you time to work on this?

---

## Comment 31

> Username: viboes  
> Created_at: 2017-09-30 14:48:06 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-333313277  

@karzhenkov The results confirm what you said.  
  
https://ci.appveyor.com/project/viboes/thread/build/1.0.111-develop

---

## Comment 32

> Username: karzhenkov  
> Created_at: 2017-09-30 18:50:34 UTC  
> Updated_at: 2017-09-30 19:09:21 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-333328382  

Well, I could spend some time to describe the potentially breaking changes. However, I would like to understand how dangerous they are.  
  
The `<threadapi>` feature is documented in `build/Jamfile.v2` only. Its declared purpose is to request a specific API. This is still correct.  
  
The value of `<threadapi>` is processed in jam-files that belong to Boost.Build and translated into dependent projects as `<define>`, `<include>` and `<library>` values in usage requirements. Macro definitions provide all the necessary information to a C ++ programmer, and nothing changes here.  
  
The changes concern users invoking `b2` from command line and programmers authoring jam-files.  
  
The former may provide `<threadapi>` value as before. User had to specify it in the case of cross-compilation. Now he is allowed to omit the explicit request. I think that it is not a breaking change, but the comment containing in `build/Jamfile.v2` should be placed to `overview.qbk` (and somewhat expanded).  
  
Perhaps some caveats are required for jam-programmers who may rely on undocumented behavior. I think that most likely there are no such projects.  
  
Do we really have potentially breaking changes?  
  
As of the work on the documentation in more general sense, I will consider this possibility, but it will not be too fast.

---

## Comment 33

> Username: viboes  
> Created_at: 2017-10-01 08:11:27 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-333360864  

You are surely right, maybe there are no such project.   
  
Don't worry for the documentation. I'll try to do my best.   
Thanks for all.

---

## Comment 34

> Username: kuhlenough  
> Created_at: 2017-10-02 17:57:29 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-333613606  

Yes, I use <threadapi> on both Windows and Linux to cross compile for VxWorks.  
In the project-config.jam  I consolidate all the cross-compile "feature" to avoid the long path lengths on windows when multiple features are specified on the b2 invocation. So ..  
  
 **b2  cross-compile=vxworks**  
  
with   
```  
import option ;  
import feature ;  
import os ;  
  
# composite feature to select all the cross compile options we need  
# as one feature, so the directory path is a reasonable length,   
# primarily for windows host where path name length matter, and due to host  
# pollution the correct options are not automatically selected  
  
feature.feature cross-compile  
    : vxworks  
    : optional composite propagated   
    ;  
  
feature.compose <cross-compile>vxworks   
    : <threadapi>pthread <binary-format>elf <abi>sysv <threading>multi <target-os>vxworks  
    ;  
  
```  
  
Auto detection with cross-compilation while admirable is often broken, I'm happy if <target-os> isn't confused with <host-os> in the jam scripts.

---

## Comment 35

> Username: viboes  
> Created_at: 2017-10-02 20:16:45 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-333651931  

Hi karzhenkov,   
  
I have a report that the develop branch is broken. The user is doing the following in a linux platform   
  
```  
b2 --build-dir=dist --with-chrono --with-thread --with-system toolset=gcc link=static variant=release stage  
```   
  
and here it is what he gets  
  
```  
Performing configuration checks  
  
    - 32-bit                   : no  
    - 64-bit                   : yes  
    - arm                      : no  
    - mips1                    : no  
    - power                    : no  
    - sparc                    : no  
    - x86                      : yes  
    - symlinks supported       : yes  
    - lockfree boost::atomic_flag : yes  
error: Name clash for '<pstage/lib>libboost_system.a'  
error:   
error: Tried to build the target twice, with property sets having   
error: these incompatible properties:  
error:   
error:     -  none  
error:     -  <threadapi>pthread  
error:   
error: Please make sure to have consistent requirements for these   
error: properties everywhere in your project, especially for install  
error: targets.  
```  
  
I'll try to reproduce this from a clean repository.   
How did you build Boost.Thread? Which command did you used?  
  
From my side I just have run the tests from the test directory as  
```  
b2 toolset=gcc  
```  
  
and every thing worked correctly.

---

## Comment 36

> Username: viboes  
> Created_at: 2017-10-02 21:29:17 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-333670810  

Adding `threadapi=pthread` to the command line solve the issue, but it shows that we have introduced a regression.

---

## Comment 37

> Username: karzhenkov  
> Created_at: 2017-10-03 17:31:03 UTC  
> Updated_at: 2017-10-03 17:58:11 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-333919486  

Hi  
  
I used standard command from Boost top-level directory:  
```  
./b2 toolset=gcc --with-thread  
```  
It works with gcc (mingw-w4) and with qcc (for QNX). The error occurs with the following command:  
```  
./b2 toolset=gcc --with-thread --with-system  
```  
This is a problem, and I have no solution yet.  
  
This command requests the compilation of `Boost.System` in two versions. The first one is explicitly specified in the command and has no `<threadapi>` in property set. The second version is dependency of `Boost.Thread` and has `<threadapi>` value derived from `<target-os>`. Both versions use the same library name in the stage directory, and we get  `error: Name clash...`  
  
In fact, these versions are identical: `Boost.System` doesn’t know anything about the `<threadapi>` feature. However, `Boost.Build` threats them as distinct just because they have different property sets.  
If we add the right value for `<threadapi>` to the command line, the property sets for both requests will be the same, and `Boost.Build` will treat both versions as one. The error will disappear.  
  
Maybe there is a way to tell `Boost.Build` not to create distinct version of `Boost.System` for different `<threadapi>` values? We need something like `incidental` attribute for `<threadapi>`, but `incidental` only for projects that do not depend on `Boost.Thread`. Theoretically, `Boost.Build` already has enough information to conclude that `<threadapi>` doesn’t  affect `Boost.System`.  
  
Another idea (also not ideal) is to include the `<conditional>@threadapi.detect` in a Jamroot. But I have some problems with jam-syntax, so I have not tested this.  
  
Maybe the Boost.Build ML will help.

---

## Comment 38

> Username: viboes  
> Created_at: 2017-10-03 21:04:16 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-333978092  

You can see my post to the ML  
  
http://boost.2283326.n4.nabble.com/PR-to-cross-compiling-Boost-Thread-Help-needed-tt4698930.html  
  
No response for the moment :(  
  
I wonder how is it that the regression test are working. Are all the testers using `threadapi=api`?  
  
If `threadapi`property cannot be deduced for Boost.Thread without requiring an explicit `threadapi=api` when other libraries as Boost.System are requested, what is the interest of deducing it for Boost.Thread?

---

## Comment 39

> Username: karzhenkov  
> Created_at: 2017-10-04 17:13:02 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-334225376  

I think Bjam is not perfect. It can't scale well, as creates new builds for older projects when new ones define their own features.  
  
Regression tests may succeed because they probably don't cause the `stage` target to be built. It is this target that leads to the name clash in the stage directory.  
  
I propose to move `<conditional>@threadapi.detect` into `Jamroot` (commit [6958cb3]( https://github.com/karzhenkov/boost/commit/6958cb3f39dd071624bf62dd7f402e4f8e532aee) in `Boost` and commit [3e381c9](https://github.com/karzhenkov/boostorg-thread/commit/3e381c9617a538c9b3d617635340d5c333a05d1d) in `Boost.Thread`). Build errors will be fixed.

---

## Comment 40

> Username: karzhenkov  
> Created_at: 2017-10-04 18:15:47 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-334244063  

@kuhlenough You probably get different names for `Boost.Thread` library when compile it on Windows and on Linux. And all the dynamic libraries that depend on `Boost.Thread` although they have the correct names, internally refer it with different names. Do you have such a problem?

---

## Comment 41

> Username: kuhlenough  
> Created_at: 2017-10-04 18:26:50 UTC  
> Updated_at: 2017-10-04 18:28:13 UTC  
> Url: https://github.com/boostorg/thread/pull/160#issuecomment-334247265  

Yes, when compiling on Windows I add.   
b2 --abbreviate-paths  --layout=system  ..  
The   _layout_ is another thing that should be associated with  target-os but is chosen by  host-os  ( perhaps for the cygwin ?)

---
