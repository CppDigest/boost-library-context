# #38 - The Module test fails on windows CI, but the error is ignored? [Open]

> Username: ClausKlein  
> Created at: 2026-01-09 17:32:10 UTC  
> Updated at: 2026-01-10 09:48:40 UTC  
> Url: https://github.com/boostorg/any/issues/38  

```  
-- `import std;` is not available  
-- Configuring incomplete, errors occurred!  
ninja: error: loading 'build.ninja': The system cannot find the file specified.  
  
  
UpdateCTestConfiguration  from :D:/a/any/boost-root/libs/any/build_module/DartConfiguration.tcl  
UpdateCTestConfiguration  from :D:/a/any/boost-root/libs/any/build_module/DartConfiguration.tcl  
Test project D:/a/any/boost-root/libs/any/build_module  
Constructing a list of tests  
Updating test list for fixtures  
Added 0 tests to meet fixture requirements  
Checking test dependency graph...  
Checking test dependency graph end  
No tests were found!!!  
Errors while running CTest  
Output from these tests are in: D:/a/any/boost-root/libs/any/build_module/Testing/Temporary/LastTest.log  
Use "--rerun-failed --output-on-failure" to re-run the failed cases verbosely.  
```

---

## Comment 1

> Username: ClausKlein  
> Created at: 2026-01-09 17:33:27 UTC  
> Url: https://github.com/boostorg/any/issues/38#issuecomment-3729915119  

see too https://github.com/ClausKlein/boost-cmake/actions/runs/20853068840/job/59912567613  
and https://github.com/ClausKlein/boost-cmake/pull/11

---

## Comment 2

> Username: apolukhin  
> Created at: 2026-01-10 09:11:29 UTC  
> Url: https://github.com/boostorg/any/issues/38#issuecomment-3732228014  

@ClausKlein unfortunately MSVC was not able to actually build modules the last time I checked. If there's a workaround I'd appreciate a PR.  
  
```  
-- Configuring incomplete, errors occurred!  
ninja: error: loading 'build.ninja': The system cannot find the file specified.  
```  
  
That's something new. Could you please provide more logs and the configure+build commands

---

## Comment 3

> Username: ClausKlein  
> Created at: 2026-01-10 09:48:40 UTC  
> Url: https://github.com/boostorg/any/issues/38#issuecomment-3732310645  

It is from your CI: https://github.com/boostorg/any/actions/runs/20689068722/job/59394456776#logs  
  
see too https://discourse.cmake.org/t/linker-error-with-cxx-modules/15449
