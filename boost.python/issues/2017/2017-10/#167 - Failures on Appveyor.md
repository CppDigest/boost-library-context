# #167 - Failures on Appveyor [Closed]

> Username: pdimov  
> Created at: 2017-10-27 15:34:29 UTC  
> Updated at: 2017-12-02 23:46:09 UTC  
> Closed at: 2017-12-02 23:46:09 UTC  
> Url: https://github.com/boostorg/python/issues/167  

See https://ci.appveyor.com/project/boostorg/boost/build/1.0.4251-develop, f.ex.  
  
https://ci.appveyor.com/project/boostorg/boost/build/1.0.4251-develop/job/flc17tpcyvvj5mad#L601

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-10-27 15:47:42 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-340009426  

Hi Peter,  
I have been observing failures like these (notably the unresolved `boost::get_pointer` symbol` errors) in my own testing with some MSVC versions (but not all !) for quite a while, but not being very familiar with MSVC, haven't been able to fix them.  
  
Are the above new failures, i.e. do they indicate some recent regression ? (I have committed some PRs recently, but don't think they could have caused this.)  
  
I'd appreciate any help in getting to the bottom of these. Could they be caused by compiler bugs (or at least, limitations that I'm not properly working around) ?  
Thanks,

---

## Comment 2

> Username: pdimov  
> Created at: 2017-10-27 15:56:53 UTC  
> Updated at: 2017-10-27 15:57:52 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-340011926  

The failures I see in this log are not about an unresolved `get_pointer`. The one in the link, which seems the most serious, is  
  
```  
capture-output bin.v2\libs\python\test\shared_ptr.test\msvc-14.1\debug\threadapi-win32\threading-multi\shared_ptr  
====== BEGIN OUTPUT ======  
running...  
**********************************************************************  
File "libs\python\test\shared_ptr.py", line 9, in __main__  
Failed example:  
    Y.store(YYY(42))  
Exception raised:  
    Traceback (most recent call last):  
      File "C:\Python27\lib\doctest.py", line 1315, in __run  
        compileflags, 1) in test.globs  
      File "<doctest __main__[1]>", line 1, in <module>  
        Y.store(YYY(42))  
    ArgumentError: Python argument types in  
        Y.store(YYY)  
    did not match C++ signature:  
        store(class std::shared_ptr<struct test_class<1> >)  
**********************************************************************  
...  
...  
...  
**********************************************************************  
1 items had failures:  
  23 of  58 in __main__  
***Test Failed*** 23 failures.  
   
EXIT STATUS: 23   
====== END OUTPUT ======  
```  
  
There are two more "LINK : fatal error LNK1207: incompatible PDB format in..." failures, which I have no clue about, and then there are  
  
```  
compile-c-c++ bin.v2\libs\python\test\calling_conventions.test\msvc-14.1\debug\threadapi-win32\threading-multi\calling_conventions.obj  
calling_conventions.cpp  
C:\projects\boost\boost/python/make_function.hpp(104): error C2672: 'boost::python::detail::get_signature': no matching overloaded function found  
C:\projects\boost\boost/python/def.hpp(82): note: see reference to function template instantiation 'boost::python::api::object boost::python::make_function<long(__stdcall *)(void)>(F)' being compiled  
        with  
        [  
            F=long (__stdcall *)(void)  
        ]  
```  
  
and  
  
```  
compile-c-c++ bin.v2\libs\python\test\calling_conventions_mf.test\msvc-14.1\debug\threadapi-win32\threading-multi\calling_conventions_mf.obj  
calling_conventions_mf.cpp  
C:\projects\boost\boost/python/class.hpp(497): error C2672: 'boost::python::detail::get_signature': no matching overloaded function found  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2017-10-27 16:02:01 UTC  
> Updated at: 2017-10-27 16:03:38 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-340013451  

Ah, I see the `get_pointer` errors, in the msvc-14.0 log. Not in msvc-12.0, they seem to be 14.0 specific.

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2017-10-27 16:18:25 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-340017457  

There is a relevant recent commit (https://github.com/boostorg/python/commit/30c9eb1fb6f0525541e4b6dd33ed01de455b0b53), though I thought that only narrowed down a conditional to more properly detect whether the compiler supported std::shared_ptr. Do you see  
anything wrong with that ?

---

## Comment 5

> Username: pdimov  
> Created at: 2017-10-27 16:33:24 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-340021014  

Looking at  
  
```  
    template <class U>  
    static inline PyTypeObject* get_derived_class_object(boost::python::detail::true_, U const volatile* x)  
    {  
        converter::registration const* r = converter::registry::query(  
            type_info(typeid(*get_pointer(x)))  
        );  
        return r ? r->m_class_object : 0;  
    }  
```  
  
in `make_ptr_instance.hpp`, it could be that the `get_pointer` in the `typeid` is (erroneously) not instantiated because the compiler thinks this is an unevaluated context. I tried to attempt a fix, but for some reason tests fail for me with  
  
```  
C:\Projects\boost-git\boost\boost/python/detail/wrap_python.hpp(50): fatal error  
 C1083: Cannot open include file: 'pyconfig.h': No such file or directory  
```  
  
even though `--debug-configuration` says  
  
```  
notice: [python-cfg] ...requested configuration matched!  
notice: [python-cfg] Details of this Python configuration:  
notice: [python-cfg]   interpreter command: "C:\Python33\python"  
notice: [python-cfg]   include path: "C:\Python33\Include"  
notice: [python-cfg]   library path: "C:\Python33\libs"  
notice: [python-cfg]   DLL search path: "C:\Python33"  
```  
  
and the `include` directory does have `pyconfig.h`.

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2017-10-27 16:37:27 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-340021966  

Would you mind posting your fix (perhaps even as a PR), so as not to get blocked by this build logic error (which seems entirely unrelated, and may not show up in CI testing). Thanks !

---

## Comment 7

> Username: pdimov  
> Created at: 2017-10-27 16:43:35 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-340023417  

I was initially going to move the `get_pointer(x)` call outside of the `typeid`, but that requires `auto`. Then I decided to just call `get_pointer(x);` outside `typeid`, to force instantiation. But on third thought, `x` is `U const volatile*` here, a raw pointer, so `get_pointer(x)` will always return `x` and there's no need to call it at all. So my final suggestion is just change it to `typeid(*x)`.

---

## Comment 8

> Username: pdimov  
> Created at: 2017-10-27 17:05:10 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-340028758  

Regarding my build problem, I indeed don't see where the include directory is supposed to be added to the `bpl-test` requirements. `py-run` adds `$(PY)`, presumably for this purpose, as the include directory is part of `/python//python` usage requirements. But I see nothing in `bpl-test` that would do so. I could be missing something though.

---

## Comment 9

> Username: stefanseefeld  
> Created at: 2017-10-27 18:05:04 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-340043626  

Thanks for the suggestion, I have tested the `get_pointer(x)`->`x` substitute above and after seeing that the missing symbol failures on MSVC 14 disappeared, pushed this to the `develop` branch. I still see test failures for the `calling_conventions` tests (again only on MSVC 14), but that's a different subject.  
I'm still not sure about the `std::shared_ptr` test failures you are reporting, they seem unrelated, too.

---

## Comment 10

> Username: pdimov  
> Created at: 2017-10-27 21:02:34 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-340084498  

The calling convention tests fail on msvc-12.0 and msvc-14.1 too, see the logs. Appveyor also has 9.0/10.0/11.0, but I don't run these on superproject updates. I'm not sure which msvc versions you support nowadays.

---

## Comment 11

> Username: stefanseefeld  
> Created at: 2017-10-28 02:57:26 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-340134731  

Looking a little more into these tests (and their failures) I notice that you are the (original) author of the tests ! :-)  
As far as I can see, the logic consists of loops over different calling conventions, for each defining a set of `get_signature()` overloads, which then get used by the inner machinery during template specialization. It looks like for some reason this machinery fails, i.e. the expected `get_signature()` overloads can't be found in the template instantiation process, leading to the wrong overload being picked up, which happens to have a different signature (the one with the second mandatory 'Target *' argument).  
Any idea why that template instantiation process may fail on a range of MSVC compilers ?

---

## Comment 12

> Username: pdimov  
> Created at: 2017-10-28 11:13:37 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-340183554  

I'm not the original author; the author (Dave Abrahams by all appearances) was just using a test of mine, `bind_stdcall_test.cpp`, as an inspiration. :-)

---

## Comment 13

> Username: pdimov  
> Created at: 2017-10-28 11:44:25 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-340185075  

This commit breaks it: https://github.com/boostorg/python/commit/b49a186b6fc4b4ecf84aa920149426a1d2385b19

---

## Comment 14

> Username: stefanseefeld  
> Created at: 2017-12-02 21:26:45 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-348721338  

@pdimov , can you please confirm the status of this ? (I'm unfortunately a bit lost in all the test results. The ones on http://www.boost.org/development/tests/develop/developer/issues.html don't seem to show them. You pointed to some appveyor tests. Where are they, and how do they differ from the "official" ones ?  
(I also reverted the commit https://github.com/boostorg/python/commit/b49a186b6fc4b4ecf84aa920149426a1d2385b19 in the hopes of fixing the `calling_conventions` tests, but still see failures on some MSVC compilers. No idea what's going on there and how to resolve this properly.)  
Thanks for your help !

---

## Comment 15

> Username: pdimov  
> Created at: 2017-12-02 21:41:08 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-348722200  

The superproject (boostorg/boost) runs Travis and Appveyor on every commit, which I've rigged to run the tests for the library mentioned in the commit message. So when the commit says "Update python from develop", the script runs `b2 libs/python/test`.  
  
The Appveyor results of that are at https://ci.appveyor.com/project/boostorg/boost/history .

---

## Comment 16

> Username: pdimov  
> Created at: 2017-12-02 21:43:49 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-348722333  

The two most recent runs for Python are https://ci.appveyor.com/project/boostorg/boost/build/1.0.4786-develop and https://ci.appveyor.com/project/boostorg/boost/build/1.0.4725-master .

---

## Comment 17

> Username: pdimov  
> Created at: 2017-12-02 21:47:24 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-348722527  

The only remaining failures there are  
  
```  
LINK : fatal error LNK1207: incompatible PDB format in 'C:\projects\boost\bin.v2\libs\python\test\msvc-12.0\debug\threadapi-win32\threading-multi\builtin_converters_ext.pdb'; delete and rebuild  
```  
  
and  
  
```  
LINK : fatal error LNK1207: incompatible PDB format in 'C:\projects\boost\bin.v2\libs\python\test\msvc-12.0\debug\threadapi-win32\threading-multi\map_indexing_suite_ext.pdb'; delete and rebuild  
```  
  
and I have no idea what causes them, or why they happen on those two particular tests.  
  
The rest of the issues we've been discussing here seem to have been cleared.

---

## Comment 18

> Username: pdimov  
> Created at: 2017-12-02 21:48:53 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-348722597  

Other than that, you have an `.appveyor.yml` file in your repo, which hints at trying to enable Appveyor, but there should be no leading dot in the name.

---

## Comment 19

> Username: pdimov  
> Created at: 2017-12-02 21:53:51 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-348722876  

This may be relevant: https://stackoverflow.com/questions/29053172/boost-python-quickstart-linker-errors

---

## Comment 20

> Username: stefanseefeld  
> Created at: 2017-12-02 22:24:20 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-348724565  

Thanks for the feedback. It sounds like we can close this issue, then. The above stackoverflow link seems to suggest a problem with `b2`, so perhaps I should follow up with those guys.  
  
Yeah, I do run my own appveyor (and travis) tests (using `Faber`). (Why do you say the leading '.' is wrong ? It's perfectly legal, and appveyor runs the builds just fine !)

---

## Comment 21

> Username: pdimov  
> Created at: 2017-12-02 22:37:47 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-348725306  

It didn't work last time I tried it, but maybe it does now.

---

## Comment 22

> Username: pdimov  
> Created at: 2017-12-02 22:46:21 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-348725751  

You still have calling convention failures in https://ci.appveyor.com/project/stefanseefeld/python/build/job/rw0mv19mvk2989yb for some reason, but I'm not seeing these failures in the superproject Appveyor run above. Feel free to close this though.

---

## Comment 23

> Username: stefanseefeld  
> Created at: 2017-12-02 23:46:09 UTC  
> Url: https://github.com/boostorg/python/issues/167#issuecomment-348728845  

I know. These are the ones I asked about earlier, as I have no clue about what's going on there.  
  
(https://www.appveyor.com/docs/build-configuration/#yaml-file-alternative-naming describes `.appveyor.yml` explicitly as a legal name.)
