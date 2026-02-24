# #55 - Facing with compilation errors while building NodeJS module with boost inside on Mac OS X [Closed]

> Username: imalyavskiy  
> Created at: 2018-11-05 11:45:20 UTC  
> Updated at: 2019-05-08 19:07:37 UTC  
> Closed at: 2019-01-08 03:44:54 UTC  
> Url: https://github.com/boostorg/process/issues/55  

MacOS Sierra 10.12  
MacOS High Sierra 10.13.6  
  
In file included from ~/boost/boost/process.hpp:24:  
In file included from ~/boost/boost/process/async_system.hpp:22:  
In file included from ~/boost/boost/process/child.hpp:21:  
In file included from ~/boost/boost/process/detail/child_decl.hpp:30:  
/boost/boost/process/detail/posix/wait_for_exit.hpp:60:7: error: expected unqualified-id  
    ::sigemptyset(&sigset);  
      ^  
/usr/include/signal.h:125:26: note: expanded from macro 'sigemptyset'  
#define sigemptyset(set)        (*(set) = 0, 0)  
                                ^  
What am I doing wrong? Any ideas? Thanks in advance.  
  
and few same errors....  
  
PS: I am using boost 1.68.0 built from sources.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-11-05 12:13:43 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-435853519  

Not your fault, didn't think that could be a macro, it's normally a function. Will fix that, do you have a full list of errors?  
  
in the meantime you can just remove the `::` before the `sigemptyset` in the headers and it should work.

---

## Comment 2

> Username: imalyavskiy  
> Created at: 2018-11-05 12:56:05 UTC  
> Updated at: 2018-11-05 13:53:45 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-435863884  

Thanks for such a quick reply. The output is right below.  
  
` shm-typed-array@ install /Users/AUser/project/SHMTypedArray  
> node-gyp rebuild  
  
  CXX(target) Release/obj.target/shm/src/gnuipcstub.o  
  CXX(target) Release/obj.target/shm/src/ipc_conductor.o  
In file included from ../src/ipc_conductor.cpp:1:  
In file included from ../src/node_shm.h:13:  
In file included from /Users/AUser/project/SHMTypedArray/boost/boost/process.hpp:24:  
In file included from /Users/AUser/project/SHMTypedArray/boost/boost/process/async_system.hpp:22:  
In file included from /Users/AUser/project/SHMTypedArray/boost/boost/process/child.hpp:21:  
In file included from /Users/AUser/project/SHMTypedArray/boost/boost/process/detail/child_decl.hpp:30:  
/Users/AUser/project/SHMTypedArray/boost/boost/process/detail/posix/wait_for_exit.hpp:60:7: error: expected unqualified-id  
    ::sigemptyset(&sigset);  
      ^  
/usr/include/signal.h:125:26: note: expanded from macro 'sigemptyset'  
#define sigemptyset(set)        (*(set) = 0, 0)  
                                ^  
In file included from ../src/ipc_conductor.cpp:1:  
In file included from ../src/node_shm.h:13:  
In file included from /Users/AUser/project/SHMTypedArray/boost/boost/process.hpp:24:  
In file included from /Users/AUser/project/SHMTypedArray/boost/boost/process/async_system.hpp:22:  
In file included from /Users/AUser/project/SHMTypedArray/boost/boost/process/child.hpp:21:  
In file included from /Users/AUser/project/SHMTypedArray/boost/boost/process/detail/child_decl.hpp:30:  
/Users/AUser/project/SHMTypedArray/boost/boost/process/detail/posix/wait_for_exit.hpp:61:7: error: expected unqualified-id  
    ::sigaddset(&sigset, SIGCHLD);  
      ^  
/usr/include/signal.h:122:31: note: expanded from macro 'sigaddset'  
#define sigaddset(set, signo)   (*(set) |= __sigbits(signo), 0)  
                                ^  
In file included from ../src/ipc_conductor.cpp:1:  
In file included from ../src/node_shm.h:13:  
In file included from /Users/AUser/project/SHMTypedArray/boost/boost/process.hpp:24:  
In file included from /Users/AUser/project/SHMTypedArray/boost/boost/process/async_system.hpp:22:  
In file included from /Users/AUser/project/SHMTypedArray/boost/boost/process/child.hpp:21:  
In file included from /Users/AUser/project/SHMTypedArray/boost/boost/process/detail/child_decl.hpp:30:  
/Users/AUser/project/SHMTypedArray/boost/boost/process/detail/posix/wait_for_exit.hpp:87:26: error: no member named 'sigtimedwait' in the global namespace  
        auto ret_sig = ::sigtimedwait(&sigset, nullptr, &ts);  
                       ~~^  
In file included from ../src/ipc_conductor.cpp:1:  
In file included from ../src/node_shm.h:13:  
In file included from /Users/AUser/project/SHMTypedArray/boost/boost/process.hpp:25:  
In file included from /Users/AUser/project/SHMTypedArray/boost/boost/process/group.hpp:32:  
/Users/AUser/project/SHMTypedArray/boost/boost/process/detail/posix/wait_group.hpp:64:7: error: expected unqualified-id  
    ::sigemptyset(&sigset);  
      ^  
/usr/include/signal.h:125:26: note: expanded from macro 'sigemptyset'  
#define sigemptyset(set)        (*(set) = 0, 0)  
                                ^  
In file included from ../src/ipc_conductor.cpp:1:  
In file included from ../src/node_shm.h:13:  
In file included from /Users/AUser/project/SHMTypedArray/boost/boost/process.hpp:25:  
In file included from /Users/AUser/project/SHMTypedArray/boost/boost/process/group.hpp:32:  
/Users/AUser/project/SHMTypedArray/boost/boost/process/detail/posix/wait_group.hpp:65:7: error: expected unqualified-id  
    ::sigaddset(&sigset, SIGCHLD);  
      ^  
/usr/include/signal.h:122:31: note: expanded from macro 'sigaddset'  
#define sigaddset(set, signo)   (*(set) |= __sigbits(signo), 0)  
                                ^  
In file included from ../src/ipc_conductor.cpp:1:  
In file included from ../src/node_shm.h:13:  
In file included from /Users/AUser/project/SHMTypedArray/boost/boost/process.hpp:25:  
In file included from /Users/AUser/project/SHMTypedArray/boost/boost/process/group.hpp:32:  
/Users/AUser/project/SHMTypedArray/boost/boost/process/detail/posix/wait_group.hpp:90:17: error: no member named 'sigtimedwait' in the global namespace  
        ret = ::sigtimedwait(&sigset, nullptr, &ts);  
              ~~^  
../src/ipc_conductor.cpp:181:10: warning: unused variable 'sz_stk' [-Wunused-variable]  
    auto sz_stk = ipc_index_[resId].size();  
         ^  
../src/ipc_conductor.cpp:180:10: warning: unused variable 'sz_ref' [-Wunused-variable]  
    auto sz_ref = ipc_index_[resId].top()->use_count();  
         ^  
../src/ipc_conductor.cpp:248:10: warning: unused variable 'sz_ref' [-Wunused-variable]  
    auto sz_ref = ipc_index_[resId].top()->use_count();  
         ^  
../src/ipc_conductor.cpp:255:14: warning: unused variable 'p' [-Wunused-variable]  
    uint8_t* p = ipc_index_[resId].top()->get<uint8_t>();  
             ^  
../src/ipc_conductor.cpp:249:10: warning: unused variable 'sz_stk' [-Wunused-variable]  
    auto sz_stk = ipc_index_[resId].size();  
         ^  
5 warnings and 6 errors generated.  
make: *** [Release/obj.target/shm/src/ipc_conductor.o] Error 1  
gyp ERR! build error  
gyp ERR! stack Error: `make` failed with exit code: 2  
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:262:23)  
gyp ERR! stack     at ChildProcess.emit (events.js:182:13)  
gyp ERR! stack     at Process.ChildProcess._handle.onexit (internal/child_process.js:240:12)  
gyp ERR! System Darwin 17.7.0  
gyp ERR! command "/usr/local/bin/node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"  
gyp ERR! cwd /Users/AUser/project/SHMTypedArray  
gyp ERR! node -v v10.13.0  
gyp ERR! node-gyp -v v3.8.0  
gyp ERR! not ok  
npm ERR! code ELIFECYCLE  
npm ERR! errno 1  
npm ERR! shm-typed-array@ install: `node-gyp rebuild`  
npm ERR! Exit status 1  
npm ERR!  
npm ERR! Failed at the shm-typed-array@ install script.  
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.  
  
npm ERR! A complete log of this run can be found in:  
npm ERR!     /Users/AUser/.npm/_logs/2018-11-04T04_54_54_133Z-debug.log  
AUsers-MBP:SHMTypedArray user$`

---

## Comment 3

> Username: imalyavskiy  
> Created at: 2018-11-07 07:04:16 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-436524951  

Hello, @klemens-morgenstern ! how is it going? Any updates?

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2018-11-08 08:03:48 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-436906441  

I just realized that `sigtimedwait` does not exist on OSX, so I have (again) no idea how to solve that, except for letting the thing go into an infinite loop until or create some workaround. That'll sadly take some time.

---

## Comment 5

> Username: imalyavskiy  
> Created at: 2018-11-08 13:29:46 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-436993790  

Does this mean that your advice above will not help?

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2018-11-08 15:32:39 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-437037903  

I fear not. Really sorry that made it into release - if you don't use the timed wait functions (wait_for / wait_until) you might just be able to comment them out. That's nothing I can use as a library solution though, obviously. I will see if I can come up with a solution.

---

## Comment 7

> Username: imalyavskiy  
> Created at: 2018-11-08 17:54:25 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-437095230  

Actually I do not use them. I use boost::interprocess but it depends on boost::process in some way and at least in 1.68. I got my module built and working with 1.66.0. So this is not critical issue to me. And I understand that bugs appear time to time... Have a good luck! And thank you for what you are doing.

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2018-11-09 03:58:27 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-437242439  

`boost::interprocess` should not depend on `boost::process`, at least that would be a change to it I am not aware off. Will keep the issue around until fixed.

---

## Comment 9

> Username: imalyavskiy  
> Created at: 2018-11-09 05:22:33 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-437253761  

Should I create an issue in boost::interprocess to mention this?

---

## Comment 10

> Username: klemens-morgenstern  
> Created at: 2018-11-09 06:50:24 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-437267287  

According to you compiler output you include it yourself in `node_shm.h:13`, and not through `interprocess`.

---

## Comment 11

> Username: imalyavskiy  
> Created at: 2018-11-09 08:36:45 UTC  
> Updated at: 2018-11-09 08:37:41 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-437288364  

Yep... you are definitely right... :-)

---

## Comment 12

> Username: imalyavskiy  
> Created at: 2018-11-10 09:12:56 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-437570033  

So... finally `boost::interprocess` does not depend on `boost::process` in any way. I used to include boost::brocess for the only thing `boost::this_process::get_id();`

---

## Comment 13

> Username: klemens-morgenstern  
> Created at: 2018-11-11 13:19:47 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-437669686  

Ah ok, well glad your problem is solved, I still need to find a fix for that. Thanks for reporting!

---

## Comment 14

> Username: pdimov  
> Created at: 2018-12-12 18:48:38 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-446699343  

The macro `sigemptyset` is still a problem in 1.69 according to @HowardHinnant.

---

## Comment 15

> Username: klemens-morgenstern  
> Created at: 2018-12-13 07:16:53 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-446866545  

Sadly, I know. Still working on the fix.

---

## Comment 16

> Username: jake-at-work  
> Created at: 2018-12-28 23:46:10 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-450444271  

Turns out that MacOS isn't the only platform that defines `sigemptyset`, `sigaddset`. That is an easy fix since all you need to do is drop the `::` qualifier. As for the `sigtimedwait`, that too isn't common on all platforms. Strangely, stripping the `::` qualifier from that one gets the code to compile. I am guessing that template is never instantiated so the fact the method does not exits isn't causing us problems.  
  
I can supply a patch but it is nothing more than stripping `::` from those 3 identifiers.

---

## Comment 17

> Username: jake-at-work  
> Created at: 2018-12-28 23:51:50 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-450444717  

Never mind, I see you have fixes for this in the develop branch already.

---

## Comment 18

> Username: klemens-morgenstern  
> Created at: 2019-01-07 10:18:15 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-451887588  

So can this issue be closed?

---

## Comment 19

> Username: imalyavskiy  
> Created at: 2019-01-07 10:49:44 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-451896161  

Up to you :)

---

## Comment 20

> Username: imalyavskiy  
> Created at: 2019-01-07 10:51:19 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-451896593  

Sorry this question was not addressed to me as I see...

---

## Comment 21

> Username: jake-at-work  
> Created at: 2019-01-07 15:21:05 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-451969170  

@klemens-morgenstern While I haven't tested your current changes I think you are good to close this. It can always be reopened if the issues persist after the next release.

---

## Comment 22

> Username: toonetown  
> Created at: 2019-01-08 22:35:56 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-452476949  

I'm also getting this when compiling some boost::asio stuff on 1.69.0.  What commit is this fixed in (I'm guessing it's post-1.69.0...)?

---

## Comment 23

> Username: toonetown  
> Created at: 2019-01-08 22:40:56 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-452478189  

I tried stripping the `::` as suggested in https://github.com/boostorg/process/issues/55#issuecomment-450444271 - however, it's still failing for me with undeclared identifier `sigtimedwait`.

---

## Comment 24

> Username: toonetown  
> Created at: 2019-01-08 22:44:26 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-452479065  

I think I found it - looks like https://github.com/boostorg/process/commit/03cba8f is the one where that is fixed.  Should I be able to just poke that into my own fork of 1.69.0, or are there more changes that the fix is dependent upon?

---

## Comment 25

> Username: klemens-morgenstern  
> Created at: 2019-01-09 05:40:16 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-452578597  

You should be able to just poke that into 1.69. Will be in 1.70.

---

## Comment 26

> Username: toonetown  
> Created at: 2019-01-09 22:22:57 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-452891568  

So - it looks like there are still some issues with this fix on macOS.  Here is what I'm seeing:  
  
 - Building with boost 1.69.0, libs/process set to https://github.com/boostorg/process/commit/c3b707b (what shipped in 1.69.0)  
    - ASIO-related code fails to compile with the unqualified-id (as described in this bug)  
 - Building with boost 1.69.0, libs/process set to https://github.com/boostorg/process/commit/03cba8f (suggested fix from this bug)  
    - ASIO-related code compiles, but running unit tests (using boost::test) seem to spawn a TON of child processes (appears to be one per test suite in my codebase) - which never exit even though the main unit test process does exit.  
 - Building with boost 1.69.0, libs/process set to https://github.com/boostorg/process/commit/e2e2b5d (current `master` branch of this repo)  
    - ASIO-related code compiles, but running unit tests (using boost::test) still spawns the child processes which never exit (same behavior as the previous case).  
 - Building with boost 1.69.0, libs/process set to https://github.com/boostorg/process/commit/44162ec (what shipped in 1.68.0)  
    - ASIO-related code compiles, and unit tests build and run as expected.  
  
Again - this is on macOS specifically.  I am willing to log the unit test issue as a separate bug - but don't know if I should log it here (the boostorg/process repo), or the upstream project (the klemens-morgenstern/boost-process repo)?  I'm also willing to try grabbing *some* version of `boost::process` and just trying it to see if it works or not...but there appear to be a lot of different branches and repos involved (so I don't know what to attempt to test against).  
  
Any guidance would be appreciated as to how/where to reproduce and report the bug against.  I'm a bit lost as to how/what is going on, because I don't ever in my code (directly) use `boost::process` - but I'm seeing side effects of this issue in both `boost::asio` and `boost::test`.

---

## Comment 27

> Username: ppLorins  
> Created at: 2019-02-13 04:00:51 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-463048400  

I'm also facing the same issue in 1.69 under OSX . Now have to roll back to some early versions.

---

## Comment 28

> Username: klemens-morgenstern  
> Created at: 2019-02-13 05:15:53 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-463061783  

@toonetown Sry, I missed the notifications for your comment.  
  
The compile issues should be fixed by now (post 1.69), I stil heard reports of runtime-errors, i.e. the `wait_for` and `wait_until` functions not working. I set up a OSX test on travis, so I hope to avoid mistakes like that in the future.

---

## Comment 29

> Username: toonetown  
> Created at: 2019-04-20 00:33:02 UTC  
> Updated at: 2019-04-20 02:27:26 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-485044029  

The unit test hang is still happening in 1.70.0.  (It does appear to happen with the `wait_for` call)

---

## Comment 30

> Username: toonetown  
> Created at: 2019-04-20 03:46:59 UTC  
> Updated at: 2019-04-20 03:47:27 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-485055755  

@klemens-morgenstern   
After some trial-and-error investigation, I think I was able to get it to work by replacing the `-15` with `SIGTERM` in both `detail/posix/wait_for_exit.hpp` and `detail/posix/wait_group.hpp`.  
  
On macOS, at least, `SIGTERM` is defined as `15`, not `-15`.  
  
I can put up a PR if you can tell me which repo to submit it against.

---

## Comment 31

> Username: klemens-morgenstern  
> Created at: 2019-04-20 05:03:57 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-485059168  

@toonetown thanks, against klemens-morgenstern/boost-process would be awesome, since that got all the CI setup.

---

## Comment 32

> Username: Nelson-numerical-software  
> Created at: 2019-05-08 19:07:37 UTC  
> Url: https://github.com/boostorg/process/issues/55#issuecomment-490612246  

Boost 1.70 not yet available on brew (always 1.69)  
Very blocking trouble :(
