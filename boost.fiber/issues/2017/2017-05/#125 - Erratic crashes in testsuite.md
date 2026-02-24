# #125 - Erratic crashes in testsuite [Closed]

> Username: DanielaE  
> Created at: 2017-05-09 09:18:43 UTC  
> Updated at: 2017-06-10 17:51:09 UTC  
> Closed at: 2017-06-10 17:51:09 UTC  
> Url: https://github.com/boostorg/fiber/issues/125  

When running fiber's testsuite, I experience erratic crashes in these test cases:  
  
- test_future_dispatch  
- test_future_post  
- test_shared_future_dispatch  
- test_shared_future_post  
  
For example, my latest run (branch `develop`) crashed in three cases:  
```  
testing.capture-output Z:\boost\bin.v2\libs\fiber\test\test_future_post.test\msvc-14.1\rls\adrs-mdl-64\lnk-sttc\thrd-mlt\test_future_post.run  
====== BEGIN OUTPUT ======  
Running 24 test cases...  
unknown location(0): fatal error: in "Boost.Fiber: future test suite/test_future_wait_until_void": memory access violation  
test_future_post.cpp(516): last checkpoint  
  
*** 1 failure is detected in the test module "Master Test Suite"  
   
EXIT STATUS: -1073741819   
====== END OUTPUT ======  
  
testing.capture-output Z:\boost\bin.v2\libs\fiber\test\test_shared_future_post.test\msvc-14.1\rls\adrs-mdl-64\lnk-sttc\thrd-mlt\test_shared_future_post.run  
====== BEGIN OUTPUT ======  
Running 22 test cases...  
unknown location(0): fatal error: in "Boost.Fiber: shared_future test suite/test_shared_future_wait_until": memory access violation  
test_shared_future_post.cpp(449): last checkpoint  
  
*** 1 failure is detected in the test module "Master Test Suite"  
   
EXIT STATUS: -1073741819   
====== END OUTPUT ======  
  
testing.capture-output Z:\boost\bin.v2\libs\fiber\test\test_shared_future_dispatch.test\msvc-14.1\rls\adrs-mdl-64\lnk-sttc\thrd-mlt\test_shared_future_dispatch.run  
====== BEGIN OUTPUT ======  
Running 22 test cases...  
unknown location(0): fatal error: in "Boost.Fiber: shared_future test suite/test_shared_future_wait_with_fiber_1": memory access violation  
test_shared_future_dispatch.cpp(508): last checkpoint  
  
*** 1 failure is detected in the test module "Master Test Suite"  
   
EXIT STATUS: -1073741819   
====== END OUTPUT ======  
```  
  
From another run earlier at the same day:  
  
```  
testing.capture-output Z:\boost\bin.v2\libs\fiber\test\test_shared_future_post.test\msvc-14.1\rls\adrs-mdl-64\lnk-sttc\thrd-mlt\test_shared_future_post.run  
====== BEGIN OUTPUT ======  
Running 22 test cases...  
unknown location(0): fatal error: in "Boost.Fiber: shared_future test suite/test_shared_future_wait_until_ref": memory access violation  
test_shared_future_post.cpp(469): last checkpoint  
  
*** 1 failure is detected in the test module "Master Test Suite"  
   
EXIT STATUS: -1073741819   
====== END OUTPUT ======  
  
testing.capture-output Z:\boost\bin.v2\libs\fiber\test\test_future_dispatch.test\msvc-14.1\rls\adrs-mdl-64\lnk-sttc\thrd-mlt\test_future_dispatch.run  
====== BEGIN OUTPUT ======  
Running 24 test cases...  
unknown location(0): fatal error: in "Boost.Fiber: future test suite/test_future_wait_with_fiber_1": memory access violation  
test_future_dispatch.cpp(535): last checkpoint  
  
*** 1 failure is detected in the test module "Master Test Suite"  
   
EXIT STATUS: -1073741819   
====== END OUTPUT ======  
```  
The test runs are conducted on Windows 10 1703, Intel Core i7-6700K (4+4 Cores at 4 GHz),  MSVC 14.1 (same with 14.0), 64 Bit, up to 6 parallel Jam tasks, parallel build, both debug and release mode. I've instrumented Boost.Test such that these errors are captured in the test log.

---

## Comment 1

> Username: olk  
> Created at: 2017-05-09 16:43:55 UTC  
> Updated at: 2017-05-09 16:44:14 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-300226823  

strange, I don't get errors using msvc-14.0

---

## Comment 2

> Username: olk  
> Created at: 2017-05-09 16:45:44 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-300227344  

do you have more detailed infos at which code line the memory access violation occurs?

---

## Comment 3

> Username: DanielaE  
> Created at: 2017-05-09 17:11:05 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-300235794  

Oliver, I can instrument these tests such that I get a full stack trace. But I'm pretty much lost what I have to change in the jam file to compile a completely different source (from my own library) into the executable and generate a release build with proper symbol info. But I'm optimimistic to figure it out 😅

---

## Comment 4

> Username: olk  
> Created at: 2017-05-10 05:57:56 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-300382328  

That's very kind, thank you!  
If you want to add shared objects (lets say abc.cpp) to the library itself you should edit build/Jamfile.v2:78:  
`lib boost_fiber  
    : numa_sources  
      abc.cpp  
      algo/algorithm.cpp`  
  
If you want to add it to the tests itself, edit test/Jamfile.v2:  
`[ run test_topology.cpp  
          abc.cpp  
    : : :  
    [ requires cxx11_auto_declarations`

---

## Comment 5

> Username: olk  
> Created at: 2017-05-10 09:20:04 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-300425328  

could you check performance/fiber/skynet_join.cpp too (would exclude boost.test as source of the problem)?

---

## Comment 6

> Username: DanielaE  
> Created at: 2017-05-10 12:17:53 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-300464320  

Ok, hilf das was?  
![skynet_join](https://cloud.githubusercontent.com/assets/1381823/25898205/6264c8e4-358b-11e7-9e7d-eb539fb7ee60.png)

---

## Comment 7

> Username: olk  
> Created at: 2017-05-26 16:06:39 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304322332  

should be fixed - can you verify it, please?

---

## Comment 8

> Username: DanielaE  
> Created at: 2017-05-26 18:07:44 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304351399  

Well, skynet_join seems to be fine now (and the rest of 'performance', too). The regular testsuite still experiences memory access violations, though. I've attached the logs from test runs with msvc 14.1 in debug and release mode for 32 and 64 bit.  
[fiber-develop.zip](https://github.com/boostorg/fiber/files/1032635/fiber-develop.zip)

---

## Comment 9

> Username: olk  
> Created at: 2017-05-26 18:51:10 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304361010  

I forgot to mention that boost.context needs to be updated too.  
On Linux I run the unit-tests with ASAN - I got not errors reported.

---

## Comment 10

> Username: olk  
> Created at: 2017-05-26 18:53:32 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304361521  

If b2 property context-impl=winfib is added, Windows-Fibers (instead of fcontext_t) are used internally.

---

## Comment 11

> Username: olk  
> Created at: 2017-05-27 06:50:10 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304432912  

On Windows-10 all unit-tests pass with msvc-14.0 and mingw-4.8.3 - do you use special compiler options?

---

## Comment 12

> Username: DanielaE  
> Created at: 2017-05-27 08:01:00 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304436459  

Ok, in general I see fewer failures now in my test runs with msvc-14.1 64bit debug+release. As you may have noticed, the failures happen only in release builds. The tests are conducted with 6 parallel jam tasks (b2 -j 6) on my 4+4 core machine (6 cores for testing boost, 2 cores for me 😄 ). If I reduce the test load to a humble 3 parallel jam tasks the failures seem to vanish (see attachment [boost-fiber.zip](https://github.com/boostorg/fiber/files/1033393/boost-fiber.zip) ).  
  
My compiler flags are a little more restrictive than the ones in vanilla Boost.Build:  
```  
"test_shared_future_dispatch.cpp" -Fo"Z:\boost\bin.v2\libs\fiber\test\test_shared_future_dispatch.test\msvc-14.1\rls\adrs-mdl-64\lnk-sttc\thrd-mlt\test_shared_future_dispatch.obj"    -TP /O2 /Ob2 /W4 /GR /MD /D_SECURE_SCL=0 /GL /Gy /Zc:forScope /Zc:wchar_t /D_SCL_SECURE_NO_WARNINGS /GF /bigobj /wd4131 /vmm /Zc:inline /Zc:rvalueCast /Gw /Zo /Zc:strictStrings /Zc:throwingNew /std:c++latest /permissive- /diagnostics:caret /favor:INTEL64 /wd4675 /wd4005 /wd4714 /wd4503 /wd4206 /wd4530 /wd4456 /wd4459 /wd4577 /wd4512 /wd4814 /EHs -c   
-DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_STATIC_LINK=1 -DBOOST_DISABLE_ASSERTS -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_SYSTEM_NO_DEPRECATED -DBOOST_SYSTEM_STATIC_LINK=1 -DBOOST_TEST_NO_AUTO_LINK=1 -DBOOST_THREAD_BUILD_LIB=1 -DBOOST_THREAD_USE_LIB=1 -DBOOST_TIMER_STATIC_LINK=1   
-DNDEBUG -D_WIN32_WINNT=0x0601 "-I..\..\.."  
```

---

## Comment 13

> Username: olk  
> Created at: 2017-05-27 18:48:33 UTC  
> Updated at: 2017-05-27 18:49:11 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304469799  

The build parameters are changed to speed/release but I can't reproduce the errors you have reported.  
I'm using a system with 32 logical CPUs:  
- Linux: gcc, clang, intel -> no issues  
            compiled with address sanitizers -> no errors reported  
- Windows-10: msvc-14.0, msvc-14.1 (+ your compiler flags), gcc-6.3 -> no errors reported  
- Solaris/Hipster: gcc-4.9.4 -> no errors reported

---

## Comment 14

> Username: DanielaE  
> Created at: 2017-05-28 05:30:55 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304494006  

Which kind of windows is that? One with WER disabled? If so you probably don't even notice the problems because no error messages are generated in the log. Before I instrumented Boost.Test-lite and Boost.Test with some runtime configuration commands to deal with program crashes such that no error popups appear and all messages get routed to the proper output channel, I had test reports falsely claiming everything is ok.

---

## Comment 15

> Username: DanielaE  
> Created at: 2017-05-28 05:36:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304494150  

I haven't really looked into the root cause, but my suspicion is a use-after-free issue under high load due to a missing synchronization somewhere. I know from my own experiences that such beasts are really hard to track down.

---

## Comment 16

> Username: olk  
> Created at: 2017-05-28 06:42:57 UTC  
> Updated at: 2017-05-28 06:43:36 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304496510  

I'm using 64bit Windows-10 (build 1607).  
  
Souldn't Boost.Test report '**passed**' or 'failed updating 1 target'?!  
  
I build with address sanitzer (of course on Linux, maybe it is avialable on Windows too):  
  
`b2 toolset=gcc cxxflags="-std=c++11 -fsanitize=address" linkflags="-lasan" -j32`  
  
```  
LD_PRELOAD=/usr/lib/libasan.so test_shared_future_post  
Running 22 test cases...  
*** No errors detected  
```  
  
The test code is single-threaded - so each run should generate an access violation.  
It is trange that I can't stress it on my system. I'm building and testing the unit-test on the command-line.  
I assume you build it in MS Visual Studio?! Could you try to use the command-line?  
  
`b2 toolset=msvc address-model=64 cxxflag="<you additional compiler flags> -j 6`

---

## Comment 17

> Username: DanielaE  
> Created at: 2017-05-28 07:04:40 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304497257  

Mine is Windows 10 64 (build 1703) but the same was happening on older Windows versions, too. Before disabling WER I had only summary failure reporting on the very last line.  
  
I do **not** build in Visual Studio. My test script (used for all Boost tests) issues `b2 -j 6 --build-dir=Z:\ --abbreviate-paths toolset=msvc-14.1 address-model=64 variant=debug,release warnings=all > z:\test.log` (in this particular case, because toolset version, address-model and variant come as arguments from the command line), executed from directory libs/fiber/test. Drive Z: is an 8 GB ramdisk (holding TEMP), everything else is on SSDs.

---

## Comment 18

> Username: olk  
> Created at: 2017-05-28 07:13:49 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304497581  

WER = Windows Error Reporting?  
-> 'Startyp' is set to 'Manuell'

---

## Comment 19

> Username: DanielaE  
> Created at: 2017-05-28 08:11:09 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304499668  

> WER = Windows Error Reporting?  
  
Correct. I have no clue what you mean with the starttype thing. This is what I do to deal with that:  
  
1. https://github.com/DanielaE/boost.core/tree/feature/suppress-error-popups  
2. https://github.com/DanielaE/boost.test/tree/feature/suppress-error-popups

---

## Comment 20

> Username: olk  
> Created at: 2017-05-28 08:17:41 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304499965  

'Startyp' == property of 'Windows Error Reporting'-service services.msc)

---

## Comment 21

> Username: DanielaE  
> Created at: 2017-05-28 08:27:20 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304500314  

Thx - yes, that's 'manuell'

---

## Comment 22

> Username: olk  
> Created at: 2017-05-28 09:09:38 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304502141  

A reason why you get access violation could be an insufficient stack size.  
I've no glue why the stack requirements should be different between build 1703 and 1607.  
  
Could you modify line 99 in file 'src/windows/stack_traits.cpp' from boost.context?  
`const std::size_t size = 128 * 1024;`  
This sets the default stacksize for a fiber to 128kB.

---

## Comment 23

> Username: DanielaE  
> Created at: 2017-05-28 09:32:01 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304503033  

No difference - as I expected. Too bad...  
  
Anyway, I will see if I can trigger the problem with a debugger attached. It will be difficult, but I will try anyway. No promises when I will come back with a result - a few hours at least!

---

## Comment 24

> Username: olk  
> Created at: 2017-05-28 09:33:37 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304503112  

thx

---

## Comment 25

> Username: DanielaE  
> Created at: 2017-05-28 15:52:40 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304523145  

Ok, I could capture access violations in two runs of `test_shared_future_dispatch` from two different test cases (see second line in the call stack). I had to resort to really dirty tricks to get there (running fiber's test suite in parallel to the stored-aside code-under-debug, and shortly breaking into the code), but this is what I am experiencing even without a debugger attached. I hope it helps in some way.  
![test_shared_future_dispatch-1](https://cloud.githubusercontent.com/assets/1381823/26530095/5e2ab8be-43ce-11e7-845e-765722438d25.png)  
![test_shared_future_dispatch-2](https://cloud.githubusercontent.com/assets/1381823/26530098/654f74e0-43ce-11e7-92c4-b87ba4ec3d87.png)

---

## Comment 26

> Username: olk  
> Created at: 2017-05-28 18:09:49 UTC  
> Updated at: 2017-05-28 18:10:10 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304530646  

Thank you for taking your time - unfortunately I've no glue why an null-pointer is mentioned. Pointer active_ctx and both parameters of wait_until() seam to be valid.

---

## Comment 27

> Username: DanielaE  
> Created at: 2017-05-29 14:19:42 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304671809  

I dug further into this: the address that's written to is taken from `active_ctx.ready_hook_.prev_`. Register R9 contains the address of `active_ctx`, RDX points to member `ready_hook_`.  
![test_shared_future_dispatch-1](https://cloud.githubusercontent.com/assets/1381823/26553125/8cb802ce-448a-11e7-8ae2-5105fcf9ea29.png)  
![test_shared_future_dispatch-2](https://cloud.githubusercontent.com/assets/1381823/26553128/9247869c-448a-11e7-8f90-028e190ac769.png)  
![test_shared_future_dispatch-3](https://cloud.githubusercontent.com/assets/1381823/26553130/96496634-448a-11e7-8cf0-94c1395b0646.png)

---

## Comment 28

> Username: olk  
> Created at: 2017-05-30 14:12:26 UTC  
> Updated at: 2017-05-30 14:16:53 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304890793  

Ich kann den Fehler nicht reproduzieren (Linux + Windows 10).  
  
Der Fehler tritt nur in der optimierten Variante auf?  
Und auch nur sporadisch?  
  
Googles gperftools (address sanitizer) haben keinen Fehler angezeigt; ebenfalls ohne Befund war Intels Inspector 2017 (Inspector 2017 hat uninitialisierten Speicher bemängelt - ist aber behoben/vermutlich false positive).  
  
Da der Test single-threaded betrieben wird, kommt eine race condition nicht in Betracht. Folglich kann es sich nur um überschriebenen Speicher handeln - allerdings hätte ich erwartet, das die gperftools bzw. Intel Inspector dies anzeigen.  
  
In den boost regression test gibt es zwei Systeme teeks99-08/teeks99-09 - test_shared_future_dispatch schlägt auf einem System fehl und auf dem anderen nicht. Dies hängig nicht von msvc-14.0 o. msvc-14.1 ab.  
Laut Tom besitzen beide Systeme die gleiche Konfiguration - einziger Unterschied ist, daß Windows auf dem einen System die CPU als Haswell erkennt (3 cores) und auf dem anderen System als 'QEMU virtual CPU 2.4' (8 cores).  
Eventuell wird, in Abhängigkeit von diesem Fakt, auf beiden Systemen unterschiedlicher Kode erzeugt - ist aber nur Spekulation.  
  
Könntest Du mir bitte mitteilen was Windows 10 bzgl. der CPU auf Deinem System anzeigt?

---

## Comment 29

> Username: DanielaE  
> Created at: 2017-05-30 16:12:06 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-304929049  

> Der Fehler tritt nur in der optimierten Variante auf?  
  
Ja.  
  
> Und auch nur sporadisch?  
  
Ja - an unterschiedlichen Testfällen, aber wie es scheint immer an derselben Stelle (die in den Bildern oben)  
  
> Könntest Du mir bitte mitteilen was Windows 10 bzgl. der CPU auf Deinem System anzeigt?  
  
Auszug aus msinfo32:  
```  
Betriebsystemname	Microsoft Windows 10 Pro	  
Version	10.0.15063 Build 15063	  
Prozessor	Intel(R) Core(TM) i7-6700K CPU @ 4.00GHz, 4008 MHz, 4 Kern(e), 8 logische(r) Prozessor(en)	  
BIOS-Modus	UEFI	  
Sicherer Startzustand	Ein	  
Zeitzone	Mitteleuropäische Sommerzeit	  
Installierter physischer Speicher (RAM)	32,0 GB	  
```  
Das ist ein Prozessor der Skylake-Generation.

---

## Comment 30

> Username: DanielaE  
> Created at: 2017-06-03 16:29:13 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-305985767  

Olli, mit dem heutigen Stand be4d5c hatte ich in mehreren Versuchen keine `memory access violation`. Statt dessen bisher einmal   
```  
testing.capture-output Z:\boost\bin.v2\libs\fiber\test\test_future_post_native.test\msvc-14.1\rls\adrs-mdl-64\cntxt-impl-wnfb\lnk-sttc\thrd-mlt\test_future_post_native.run  
====== BEGIN OUTPUT ======  
Running 24 test cases...  
test_future_post.cpp(522): error: in "Boost.Fiber: future test suite/test_future_wait_until_void": check boost::fibers::future_status::ready == status has failed  
```  
Vielleicht hilft Dir das!

---

## Comment 31

> Username: olk  
> Created at: 2017-06-03 18:20:09 UTC  
> Updated at: 2017-06-03 20:26:51 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-305992517  

Interessant, commit 4ca8bc hat eine Unzulänglichkeit bzgl. wait_until() behoben. Vielleicht hatte diese die access violations verursacht.  
Ich schaue mir Testschritt `test_future_wait_until_void` 'mal an.  
Viele Grüße und schönes Pfingstfest!

---

## Comment 32

> Username: DanielaE  
> Created at: 2017-06-05 07:40:30 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-306124195  

Moin Olli, ich habe mal ein paar Testläufe mit der 1b1ec0 gemacht. Ergebnis im Anhang - viel Spaß!  
[test-fiber-1b1ec0.zip](https://github.com/boostorg/fiber/files/1051383/test-fiber-1b1ec0.zip)

---

## Comment 33

> Username: DanielaE  
> Created at: 2017-06-07 18:09:48 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-306878584  

Hi Olli, mit `be5f5b` ist alles schick - keinerlei Probleme mehr (abgesehen von den Compilerwarnungen, die ich in meinem Feature-Branch dazu passend verarzte). 😄

---

## Comment 34

> Username: olk  
> Created at: 2017-06-08 06:07:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-307007928  

Hallo Daniela, vielen Dank, daß Du so fleißig testest.  
Für einige unit-tests habe ich die timeout-Werte angepaßt (waren für coop. scheduling zu eng gefaßt) - test_mutex* steht noch aus.  
Bzgl. Compilerwarnungen hatte ich bereits Anpassungen vorgenommen (msvc-14.1) - da schau' ich nochmals nach.

---

## Comment 35

> Username: DanielaE  
> Created at: 2017-06-08 06:17:41 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-307009539  

Moin Olli!  
  
Gerne doch. Das Ziel für meine In-Haus-Distro von Boost möglichst wenige Fehler bei **allen Tests** zu haben (und nicht nur dem **einen Test** in der Testmatrix) und zusätzlich **keine Warnungen** in diesen Tests in Compiler-Warnstufe /W4. Diese Stufe erfüllt die in MSVC mitgelieferte Standardbibliothek.  
  
Laß dich einfach von https://github.com/DanielaE/boost.fiber/commit/135750476473541249fcc3b0295d03b877703e7f inspirieren.

---

## Comment 36

> Username: olk  
> Created at: 2017-06-08 21:28:50 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-307232690  

Die unit-tests sind nun entsprechend angepaßt; skynet-Microbenchmark habe ich wieder auf 4kB pro Stack gesetzt.

---

## Comment 37

> Username: DanielaE  
> Created at: 2017-06-10 08:50:34 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-307552199  

Alles gut! Für mich ist der Kaas gegessen 😁

---

## Comment 38

> Username: olk  
> Created at: 2017-06-10 17:51:09 UTC  
> Url: https://github.com/boostorg/fiber/issues/125#issuecomment-307580328  

gut, dann schließe ich den Bug - vielen Dank für Deine tolle Unterstützung   :heart_eyes:
