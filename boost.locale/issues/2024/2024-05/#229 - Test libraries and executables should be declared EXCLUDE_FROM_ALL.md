# #229 - Test libraries and executables should be declared EXCLUDE_FROM_ALL [Closed]

> Username: pdimov  
> Created at: 2024-05-14 18:10:13 UTC  
> Updated at: 2024-05-16 11:34:24 UTC  
> Closed at: 2024-05-16 11:34:24 UTC  
> Url: https://github.com/boostorg/locale/issues/229  

The Boost convention is that `cmake --build .` only builds the libraries. Test libraries and executables are built with `cmake --build . --target tests`.

---

## Comment 1

> Username: Flamefire  
> Created at: 2024-05-14 20:27:10 UTC  
> Url: https://github.com/boostorg/locale/issues/229#issuecomment-2111083539  

Isn't this the case already through use of `boost_test`/`boost_test_jamfile`?  
  
Or did I miss any?  
  
https://github.com/boostorg/locale/blob/f7b1de540351855b0d24d302224b859e958497c6/test/CMakeLists.txt#L39-L44

---

## Comment 2

> Username: pdimov  
> Created at: 2024-05-14 23:37:57 UTC  
> Url: https://github.com/boostorg/locale/issues/229#issuecomment-2111329806  

Oh, that's examples, not tests.  
```  
[ 43%] Building CXX object libs/locale/examples/CMakeFiles/boost_locale-expl_boundary.dir/boundary.cpp.o  
[ 43%] Linking CXX executable ../../../stage/bin/boost_locale-expl_boundary  
[ 43%] Built target boost_locale-expl_boundary  
[ 43%] Building CXX object libs/locale/examples/CMakeFiles/boost_locale-expl_calendar.dir/calendar.cpp.o  
[ 43%] Linking CXX executable ../../../stage/bin/boost_locale-expl_calendar  
[ 43%] Built target boost_locale-expl_calendar  
[ 43%] Building CXX object libs/locale/examples/CMakeFiles/boost_locale-expl_collate.dir/collate.cpp.o  
[ 43%] Linking CXX executable ../../../stage/bin/boost_locale-expl_collate  
[ 43%] Built target boost_locale-expl_collate  
[ 43%] Building CXX object libs/locale/examples/CMakeFiles/boost_locale-expl_conversions.dir/conversions.cpp.o  
[ 43%] Linking CXX executable ../../../stage/bin/boost_locale-expl_conversions  
[ 43%] Built target boost_locale-expl_conversions  
[ 43%] Building CXX object libs/locale/examples/CMakeFiles/boost_locale-expl_hello.dir/hello.cpp.o  
[ 43%] Linking CXX executable ../../../stage/bin/boost_locale-expl_hello  
[ 43%] Built target boost_locale-expl_hello  
[ 43%] Building CXX object libs/locale/examples/CMakeFiles/boost_locale-expl_wboundary.dir/wboundary.cpp.o  
[ 43%] Linking CXX executable ../../../stage/bin/boost_locale-expl_wboundary  
[ 43%] Built target boost_locale-expl_wboundary  
[ 43%] Building CXX object libs/locale/examples/CMakeFiles/boost_locale-expl_wconversions.dir/wconversions.cpp.o  
[ 43%] Linking CXX executable ../../../stage/bin/boost_locale-expl_wconversions  
[ 43%] Built target boost_locale-expl_wconversions  
[ 43%] Building CXX object libs/locale/examples/CMakeFiles/boost_locale-expl_whello.dir/whello.cpp.o  
[ 43%] Linking CXX executable ../../../stage/bin/boost_locale-expl_whello  
[ 43%] Built target boost_locale-expl_whello  
[ 43%] Building CXX object libs/locale/examples/CMakeFiles/boost_locale-expl_perf_collate.dir/performance/perf_collate.cpp.o  
[ 43%] Linking CXX executable ../../../stage/bin/boost_locale-expl_perf_collate  
[ 43%] Built target boost_locale-expl_perf_collate  
[ 43%] Building CXX object libs/locale/examples/CMakeFiles/boost_locale-expl_perf_convert.dir/performance/perf_convert.cpp.o  
[ 43%] Linking CXX executable ../../../stage/bin/boost_locale-expl_perf_convert  
[ 43%] Built target boost_locale-expl_perf_convert  
[ 43%] Building CXX object libs/locale/examples/CMakeFiles/boost_locale-expl_perf_format.dir/performance/perf_format.cpp.o  
[ 43%] Linking CXX executable ../../../stage/bin/boost_locale-expl_perf_format  
[ 43%] Built target boost_locale-expl_perf_format  
```  
Not sure what we're supposed to do with these. I usually treat them as compile tests.

---

## Comment 3

> Username: Flamefire  
> Created at: 2024-05-15 07:30:10 UTC  
> Url: https://github.com/boostorg/locale/issues/229#issuecomment-2111782465  

In the context of what we have right now it makes sense then I guess  
I'm still wondering WHY we do that.  
  
The usual workflow for pretty much any software I built so far (and that's a lot given I'm co-maintaining an HPC cluster) is: `configure && make && make test && make install` (or the equivalent on other buildsystems, e.g. `cmake` instead of `configure`)  
  
With EXCLUDE_FROM_ALL we break this "convention" by having `make test` (or `ctest .`) be a silent no-op unless `make tests` is executed before.  
  
I try to go for the least element of surprise and deviating from semi-standard behavior, especially if it leads to **silent** unexpected results, is surprising.  
  
So what was the motivation? IMO building ALL with `make all` serves as a smoke test if the library works on the current system/compiler/..., i.e. there are no unmet expectations on that system from the library dev.  
And when adding/changing something myself I use the ALL target to quickly see if I missed anything obvious. I'll now need to remember to use the `tests` target for that.  
Finally having `make tests` and `make test` might be confusing

---

## Comment 4

> Username: pdimov  
> Created at: 2024-05-15 07:40:12 UTC  
> Url: https://github.com/boostorg/locale/issues/229#issuecomment-2111799303  

No, `ctest` is not a silent no-op, all the tests that haven't been built fail, because the executables aren't there.  
  
> IMO building ALL with make all serves as a smoke test if the library works on the current system/compiler/...  
  
But that's not what it does at all. It builds all of Boost, not "the library".

---

## Comment 5

> Username: Flamefire  
> Created at: 2024-05-15 10:07:30 UTC  
> Url: https://github.com/boostorg/locale/issues/229#issuecomment-2112098987  

> No, ctest is not a silent no-op, all the tests that haven't been built fail, because the executables aren't there.  
  
You are right. I confused it with the case that no tests were added at all in which case ctest will succeed without having run anything. There is the flag `--no-tests=error` to detect that case, which can happen if ENABLE_TESTING was accidentally disable somewhere.

---

## Comment 6

> Username: pdimov  
> Created at: 2024-05-15 12:30:49 UTC  
> Url: https://github.com/boostorg/locale/issues/229#issuecomment-2112398454  

--output-on-failure and --no-tests=error should have been the default, really. I have a `check` target that runs ctest with these, but the problem with it is that there's no good way to pass the correct -j to it.
