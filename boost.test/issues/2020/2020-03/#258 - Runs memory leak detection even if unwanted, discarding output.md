# #258 - Runs memory leak detection even if unwanted, discarding output [Open]

> Username: GyrosGeier  
> Created at: 2020-03-05 11:36:42 UTC  
> Updated at: 2020-04-21 14:23:11 UTC  
> Url: https://github.com/boostorg/test/issues/258  

I'm running tests under DrMemory to find leaks, and find that the builtin leak detection interferes with that so I'd like to turn it off. I've tried  
  
    set BOOST_DETECT_MEMORY_LEAK=0  
  
which hides the output, but the process still spends a minute below `_CrtDumpMemoryLeaks` during shutdown, after printing results. Running under DrMemory, that often extends to an hour, causing timeouts.  
  
I'd expect VS based memory leak debugging to be disabled completely in this case.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2020-04-08 17:48:24 UTC  
> Url: https://github.com/boostorg/test/issues/258#issuecomment-611098843  

What if you use `BOOST_TEST_DETECT_MEMORY_LEAK` instead?  (see [here](https://www.boost.org/doc/libs/1_72_0/libs/test/doc/html/boost_test/utf_reference/rt_param_reference/detect_memory_leaks.html)).

---

## Comment 2

> Username: GyrosGeier  
> Created at: 2020-04-08 19:28:08 UTC  
> Url: https://github.com/boostorg/test/issues/258#issuecomment-611147532  

Sorry, that is what I use. The script fragment is  
  
```  
cmake --build . --config %config% --target install -- /M  
if ERRORLEVEL 1 exit /b %ERRORLEVEL%  
  
:: disable Windows CRT memory leak checker to avoid stacking checkers  
set BOOST_TEST_DETECT_MEMORY_LEAK=0  
  
ctest --no-compress-output -T test -C %config% -j%NUMBER_OF_PROCESSORS%  
ctest --no-compress-output -T memcheck -C %config% -j%NUMBER_OF_PROCESSORS% --timeout 3600  
  
exit /b 0  
```  
There is no leak detection output in the console log or the test reports, but it still takes ages, e.g. [Debug](https://jenkins.simonrichter.eu/job/windows-kicad-msvc-head/build=debug,cpu=x86,label=msvc/lastSuccessfulBuild/console) build  
  
```  
19:09:12 C:\Jenkins\workspace\windows-kicad-msvc-head\build\debug\cpu\x86\label\msvc\build>ctest --no-compress-output -T test -C Debug -j32   
19:09:20    Site: BUILD-PC  
19:09:20    Build name: Win32-MSBuild  
19:09:25 Create new tag: 20200408-1705 - Experimental  
19:09:25 Test project C:/Jenkins/workspace/windows-kicad-msvc-head/build/debug/cpu/x86/label/msvc/build  
19:09:26     Start 1: common_eeschema  
19:09:27     Start 2: common_pcbnew  
19:09:27     Start 3: qa_common_gerbview  
19:09:27     Start 4: pcbnew  
19:09:27     Start 5: eeschema  
19:09:27     Start 6: sexpr  
19:09:27     Start 7: kmath  
19:09:27     Start 8: kicad2step  
19:09:47 1/8 Test #7: kmath ............................   Passed   20.13 sec  
19:09:47 2/8 Test #6: sexpr ............................   Passed   20.26 sec  
19:09:47 3/8 Test #8: kicad2step .......................   Passed   20.19 sec  
19:21:53 4/8 Test #3: qa_common_gerbview ...............   Passed  746.09 sec  
19:21:54 5/8 Test #5: eeschema .........................   Passed  746.77 sec  
19:21:54 6/8 Test #1: common_eeschema ..................   Passed  748.01 sec  
19:21:55 7/8 Test #2: common_pcbnew ....................   Passed  748.84 sec  
19:21:56 8/8 Test #4: pcbnew ...........................   Passed  749.42 sec  
19:21:56   
19:21:56 100% tests passed, 0 tests failed out of 8  
19:21:56   
19:21:56 Total Test time (real) = 751.21 sec  
19:21:56   
19:21:56 C:\Jenkins\workspace\windows-kicad-msvc-head\build\debug\cpu\x86\label\msvc\build>ctest --no-compress-output -T memcheck -C Debug -j32 --timeout 3600   
19:21:56    Site: BUILD-PC  
19:21:56    Build name: Win32-MSBuild  
19:21:56 Memory check project C:/Jenkins/workspace/windows-kicad-msvc-head/build/debug/cpu/x86/label/msvc/build  
19:21:56     Start 4: pcbnew  
19:21:56     Start 2: common_pcbnew  
19:21:56     Start 1: common_eeschema  
19:21:56     Start 5: eeschema  
19:21:56     Start 3: qa_common_gerbview  
19:21:56     Start 6: sexpr  
19:21:57     Start 8: kicad2step  
19:21:57     Start 7: kmath  
19:22:15 1/8 MemCheck #7: kmath ............................   Passed   18.94 sec  
19:22:21 2/8 MemCheck #6: sexpr ............................   Passed   24.09 sec  
19:22:52 3/8 MemCheck #8: kicad2step .......................   Passed   55.81 sec  
19:45:29 4/8 MemCheck #3: qa_common_gerbview ...............   Passed  1411.70 sec  
19:46:00 5/8 MemCheck #1: common_eeschema ..................   Passed  1443.31 sec  
19:46:09 6/8 MemCheck #4: pcbnew ...........................   Passed  1452.74 sec  
19:46:11 7/8 MemCheck #2: common_pcbnew ....................   Passed  1454.61 sec  
19:47:11 8/8 MemCheck #5: eeschema .........................   Passed  1514.47 sec  
19:47:11   
19:47:11 100% tests passed, 0 tests failed out of 8  
```  
vs [Release](https://jenkins.simonrichter.eu/job/windows-kicad-msvc-head/build=release,cpu=x86,label=msvc/lastSuccessfulBuild/console) build  
```  
19:11:29 C:\Jenkins\workspace\windows-kicad-msvc-head\build\release\cpu\x86\label\msvc\build>ctest --no-compress-output -T test -C Release -j32   
19:11:29    Site: BUILD-PC  
19:11:29    Build name: Win32-MSBuild  
19:11:29 Create new tag: 20200408-1707 - Experimental  
19:11:29 Test project C:/Jenkins/workspace/windows-kicad-msvc-head/build/release/cpu/x86/label/msvc/build  
19:11:29     Start 1: common_eeschema  
19:11:29     Start 2: common_pcbnew  
19:11:29     Start 3: qa_common_gerbview  
19:11:29     Start 4: pcbnew  
19:11:29     Start 5: eeschema  
19:11:29     Start 6: sexpr  
19:11:29     Start 7: kmath  
19:11:29     Start 8: kicad2step  
19:11:29 1/8 Test #7: kmath ............................   Passed    0.09 sec  
19:11:29 2/8 Test #6: sexpr ............................   Passed    0.13 sec  
19:11:29 3/8 Test #8: kicad2step .......................   Passed    0.19 sec  
19:11:30 4/8 Test #3: qa_common_gerbview ...............   Passed    1.55 sec  
19:11:30 5/8 Test #1: common_eeschema ..................   Passed    1.62 sec  
19:11:30 6/8 Test #2: common_pcbnew ....................   Passed    1.59 sec  
19:11:31 7/8 Test #5: eeschema .........................   Passed    1.62 sec  
19:11:31 8/8 Test #4: pcbnew ...........................   Passed    1.72 sec  
19:11:31   
19:11:31 100% tests passed, 0 tests failed out of 8  
19:11:31   
19:11:31 Total Test time (real) =   1.96 sec  
19:11:31   
19:11:31 C:\Jenkins\workspace\windows-kicad-msvc-head\build\release\cpu\x86\label\msvc\build>ctest --no-compress-output -T memcheck -C Release -j32 --timeout 3600   
19:11:31    Site: BUILD-PC  
19:11:31    Build name: Win32-MSBuild  
19:11:31 Memory check project C:/Jenkins/workspace/windows-kicad-msvc-head/build/release/cpu/x86/label/msvc/build  
19:11:31     Start 4: pcbnew  
19:11:31     Start 5: eeschema  
19:11:31     Start 1: common_eeschema  
19:11:31     Start 2: common_pcbnew  
19:11:31     Start 3: qa_common_gerbview  
19:11:31     Start 8: kicad2step  
19:11:31     Start 6: sexpr  
19:11:31     Start 7: kmath  
19:11:41 1/8 MemCheck #7: kmath ............................   Passed    9.72 sec  
19:11:42 2/8 MemCheck #8: kicad2step .......................   Passed   10.85 sec  
19:11:42 3/8 MemCheck #6: sexpr ............................   Passed   11.48 sec  
19:29:27 4/8 MemCheck #3: qa_common_gerbview ...............   Passed  1076.18 sec  
19:29:28 5/8 MemCheck #2: common_pcbnew ....................   Passed  1076.66 sec  
19:42:21 6/8 MemCheck #5: eeschema .........................   Passed  1849.83 sec  
20:14:30 7/8 MemCheck #4: pcbnew ...........................***Timeout 3778.72 sec  
20:14:30 8/8 MemCheck #1: common_eeschema ..................***Timeout 3778.70 sec  
20:14:30   
20:14:30 75% tests passed, 2 tests failed out of 8  
```  
That some tests take longer under DrMemory is curious, but probably unrelated, because these processes are spending their time within some DrMemory helper functions, not in the MSVCRT memory allocation dumper.  
The interesting bit to me is that running the Debug binary without DrMemory already takes 20 seconds when the Release build needs 80ms.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2020-04-08 20:45:55 UTC  
> Url: https://github.com/boostorg/test/issues/258#issuecomment-611185461  

Thanks for the quick reply.   
  
If you pass that `BOOST_TEST_DETECT_MEMORY_LEAK=0` or if you do not pass it at all, it should have the same effect. Even if `BOOST_TEST_DETECT_MEMORY_LEAK` is not propagated properly to the boost.test module from cmake/ctest (you may see that by just doing `ctest -V` and it should print you the environment), the observations should be the same.  
  
Coming to your original issue: there is no call to `CrtDumpMemoryLeaks` in boost.test code (as of today/master). I am wondering what made you observe time spent there.  
  
Debug builds on Windows are particularly slow I have to say. But from your reports, the release/memcheck is timing out. Is that correct? If this is the issue you want to solve, I am afraid I will not be able to help much, but also as you said it seems to be a `DrMemory` issue.  
  
There may be some overhead of Boost.Test when executing on Debug. One way to reduce this overhead would be to recompile boost.test with optimizations, static or dll linking (header variant will not achieve this), and still linking with the debug CRT. This should help reducing the overhead of Boost.Test while keeping the nice debugging possibility of the libraries under test.

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2020-04-08 20:50:17 UTC  
> Url: https://github.com/boostorg/test/issues/258#issuecomment-611187428  

You may use [this flag](https://boostorg.github.io/build/manual/develop/index.html#bbv2.builtin.features.cflags) if you want to compile Boost.Test with `/O2` or `/O3`.

---

## Comment 5

> Username: GyrosGeier  
> Created at: 2020-04-09 14:14:53 UTC  
> Url: https://github.com/boostorg/test/issues/258#issuecomment-611550965  

The `_CrtDumpMemoryLeaks` is called from CRT exit handling, if `_CRTDBG_LEAK_CHECK_DF` is set using `_CrtSetDbgFlag` at some point, disabling leak detection should make sure it is disabled.  
  
When I manually run a test program (compiled in Debug mode), it takes about 20 seconds to finish, during that time I can use Process Explorer to get a stack snapshot, and see that it's indeed spending all the time there.  
  
If I don't explicitly pass `BOOST_TEST_DETECT_MEMORY_LEAK=0`, I also get a few megabytes of memory leak debug information on the console.  
  
To isolate this problem, I've built a rather small test program:  
  
```  
#define BOOST_TEST_MODULE leak_test  
#include <boost/test/unit_test.hpp>  
BOOST_AUTO_TEST_SUITE( leak_test );  
BOOST_AUTO_TEST_CASE( do_leak_test ) { int *i = new int; }  
BOOST_AUTO_TEST_SUITE_END()  
```  
Running this outputs  
```  
l>leak_test.exe  
Running 1 test case...  
  
*** No errors detected  
Detected memory leaks!  
Dumping objects ->  
{6390} normal block at 0x004B4548, 4 bytes long.  
 Data: <    > CD CD CD CD  
Object dump complete.  
```  
so CRT leak detection is definitely enabled by default. That test program needs 0.06s normally, and 11s with DrMemory (and it generates a [number of complaints](https://github.com/boostorg/test/files/4456619/results.txt)).  
  
Boost was [built](https://jenkins.simonrichter.eu/view/MSVC/job/windows-boost-msvc/build=debug,cpu=x86,label=msvc/177/console) in Debug mode, using  
  
```  
toolset=msvc  
architecture=x86  
address-model=32  
link=shared  
threading=multi  
variant=debug  
runtime-link=shared  
--build-type=complete  
```

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2020-04-09 15:57:47 UTC  
> Url: https://github.com/boostorg/test/issues/258#issuecomment-611606762  

Thank you for the details. According to [this](https://docs.microsoft.com/en-us/cpp/c-runtime-library/reference/crtsetdbgflag?view=vs-2019) the default for `_CRTDBG_ALLOC_MEM_DF` is `ON`. So this is not explicitly boost.test that sets this bit but the CRT. I believe you would have the same report result by creating a leak in a normal program.  
  
That is to say this is not boost.test that enables this. OTOH, it is not disabling it if `BOOST_TEST_DETECT_MEMORY_LEAK=0` and leaves the default. Changing this behaviour may affect other usages.   
  
What we can possibly do is to pass something like `-1` to force disabling it, leaving the value `0` to the default behaviour. Would that work for you?

---

## Comment 7

> Username: GyrosGeier  
> Created at: 2020-04-09 16:36:12 UTC  
> Url: https://github.com/boostorg/test/issues/258#issuecomment-611626019  

Would probably work for me, but that would kind of contradict the documentation that says that a value of `0` disables leak detection, since it doesn't.  
  
Looking at the `detect_memory_leaks` function, it also seems that it is intended that passing `on_off = false` disables leak detection explicitly:  
  
```  
    int flags = _CrtSetDbgFlag( _CRTDBG_REPORT_FLAG );  
  
    if( !on_off )  
        flags &= ~_CRTDBG_LEAK_CHECK_DF;  
```  
  
but the only call site never calls it that way:  
```  
    unsigned long detect_mem_leak = runtime_config::get<unsigned long>( runtime_config::btrt_detect_mem_leaks );  
    if( detect_mem_leak > 0 ) {  
        debug::detect_memory_leaks( true, runtime_config::get<std::string>( runtime_config::btrt_report_mem_leaks ) );  
        debug::break_memory_alloc( (long)detect_mem_leak );  
    }  
```  
  
What I don't understand yet is why I don't get output to the console when I set the environment variable to `0`, because in principle that code should not be doing anything.

---

## Comment 8

> Username: raffienficiaud  
> Created at: 2020-04-09 17:24:09 UTC  
> Url: https://github.com/boostorg/test/issues/258#issuecomment-611650108  

> Would probably work for me, but that would kind of contradict the documentation that says that a value of `0` disables leak detection, since it doesn't.  
  
Right I thought that we can leave `BOOST_TEST_DETECT_MEMORY_LEAK=0 => default behaviour`, but the default behaviour can just be not passing any of the `BOOST_TEST_DETECT_MEMORY_LEAK` variables, although I am not sure with the current state of the cla if this is feasible.  
  
So this is a bug as it does not explicitly disable the mem leak with this variable equals 0, and leaves it to the default behaviour which is, in your case, conflicting with other things.

---

## Comment 9

> Username: GyrosGeier  
> Created at: 2020-04-09 18:14:18 UTC  
> Url: https://github.com/boostorg/test/issues/258#issuecomment-611676654  

That, and I'm also wary of how giving that environment variable still somehow suppresses output of debug information to the console, even though it is generated -- that might be a hidden bug as well, because the only place where the report is explicitly redirected is the function that isn't called.

---

## Comment 10

> Username: GyrosGeier  
> Created at: 2020-04-09 18:16:24 UTC  
> Url: https://github.com/boostorg/test/issues/258#issuecomment-611677804  

Also, I just saw that the documentation for `_CrtSetDbgFlag` states that the `_CRTDBG_LEAK_CHECK_DF` defaults OFF -- the only thing that is ON by default is writing down the allocation information so the program can later decide to enable the report.

---

## Comment 11

> Username: raffienficiaud  
> Created at: 2020-04-09 20:45:05 UTC  
> Url: https://github.com/boostorg/test/issues/258#issuecomment-611743941  

I do not see any other place in the code that calls any function starting with `_Crt`. I will investigate.
