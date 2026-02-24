# #86 Don't add all libraries when BOOST_INCLUDE_LIBRARIES is set [Merged]

> Username: Flamefire  
> Created at: 2025-09-10 14:59:36 UTC  
> Updated at: 2025-09-22 07:30:19 UTC  
> Merged at: 2025-09-18 16:11:30 UTC  
> Closed at: 2025-09-18 16:11:30 UTC  
> Url: https://github.com/boostorg/cmake/pull/86  

Previously all libraries were added even though only a subset was selected. This makes the configure process much longer then required or may fail for libraries that aren't intended to be build.  
  
Instead gather a 2nd list of dependencies determined by the test/CMakeLists.txt when BUILD_TESTING is ON and add only those (with disabled install and tests)  
  
For that I factored out the collection of dependencies, which also allows to use "clean" variable names, for reuse when collecting both lists  
  
  
Use case is a user who wanted to build (and test) Boost.JSON but Boost.Parser requires e.g. a higher CMake version which fails the configure process even though Boost.Parser is not required for Boost.JSON:  
`cmake ~/git/boost -DBUILD_TESTING=Y -DBOOST_INCLUDE_LIBRARIES=json -DBoost_DEBUG=Y`  
  
Now:  
```  
-- Scanning dependencies: json  
-- Scanning dependencies: align;assert;config;container;container_hash;core;describe;endian;mp11;system;throw_exception  
-- Scanning dependencies: static_assert;intrusive;move;variant2;winapi  
-- Scanning dependencies: predef  
-- Scanning dependencies: json;align;assert;config;container;container_hash;core;describe;endian;mp11;system;throw_exception;static_assert;intrusive;move;variant2;winapi;predef  
  
-- Enabling installation for 'container'  
-- Adding Boost dependency container_hash  
-- Enabling installation for 'container_hash'  
-- Adding Boost dependency core  
-- Enabling installation for 'core'  
-- Adding Boost dependency describe  
-- Enabling installation for 'describe'  
-- Adding Boost dependency endian  
-- Enabling installation for 'endian'  
-- Skipping Boost library graph_parallel, BOOST_ENABLE_MPI is OFF  
-- Adding Boost dependency headers  
-- Enabling installation for 'headers'  
-- Adding Boost dependency intrusive  
-- Enabling installation for 'intrusive'  
-- Adding Boost library json  
-- Enabling installation for 'json'  
-- Adding Boost dependency move  
-- Enabling installation for 'move'  
-- Adding Boost dependency mp11  
-- Enabling installation for 'mp11'  
-- Skipping Boost library mpi, BOOST_ENABLE_MPI is OFF  
-- Skipping Boost library parameter_python, BOOST_ENABLE_PYTHON is OFF  
-- Adding Boost dependency predef  
-- Enabling installation for 'predef'  
-- Skipping Boost library property_map_parallel, BOOST_ENABLE_MPI is OFF  
-- Skipping Boost library python, BOOST_ENABLE_PYTHON is OFF  
-- Adding Boost dependency static_assert  
-- Enabling installation for 'static_assert'  
-- Adding Boost dependency system  
-- Enabling installation for 'system'  
-- Adding Boost dependency throw_exception  
-- Enabling installation for 'throw_exception'  
-- Adding Boost dependency variant2  
-- Enabling installation for 'variant2'  
-- Adding Boost dependency winapi  
-- Enabling installation for 'winapi'  
```  
  
Fixes #17

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-09-10 15:22:45 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3275455482  

I don't think that we should impose the "one library per line" requirement on all test/CML files. We allow more freedom there.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2025-09-10 15:52:12 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3275560183  

> I don't think that we should impose the "one library per line" requirement on all test/CML files. We allow more freedom there.  
  
I enhanced the scanner to allow multiple Boost libs per line.  
  
That also fixes https://github.com/boostorg/cmake/issues/17

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-09-10 19:43:13 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3276287237  

```  
CMake Error at libs/mysql/test/CMakeLists.txt:33 (target_link_libraries):  
  The link interface of target "boost_mysql_testing" contains:  
  
    Boost::unit_test_framework  
  
  but the target was not found.  
```  
Looks like we need to do a bit of special casing.

---

## Comment 4

> Username: nigels-com  
> Created_at: 2025-09-10 20:32:52 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3276412199  

MS Copilot seems "pleased".  
  
Here’s a review of PR [#86](https://github.com/boostorg/cmake/pull/86): "Don't add all libraries when BOOST_INCLUDE_LIBRARIES is set".  
  
---  
  
## Summary  
  
This PR significantly improves how Boost's CMake superproject handles the `BOOST_INCLUDE_LIBRARIES` variable:  
  
- **Before:** Even when `BOOST_INCLUDE_LIBRARIES` was set, all libraries would be added, with non-included ones being excluded from all.  
- **After:** Only explicitly included libraries, their dependencies, and their test dependencies (when enabled) are added. Unrelated libraries are no longer present in the build system at all.  
  
---  
  
## Key Changes  
  
1. **Dependency Scanning Refactor:**  
   - The function `__boost_scan_dependencies` is enhanced to:  
     - Support optional subfolder scanning (for test dependencies).  
     - Handle multiple matches on a line.  
   - Added a new function `__boost_gather_dependencies` to recursively collect dependencies for the included libraries, and their test dependencies (if `BUILD_TESTING` is enabled).  
  
2. **Library Inclusion Logic:**  
   - If `BOOST_INCLUDE_LIBRARIES` is set:  
     - Only included libraries, their dependencies, and (optionally) their test dependencies are added.  
     - Libraries not in these sets are not added at all.  
   - The previous logic would include all libraries, marking non-included ones as `EXCLUDE_FROM_ALL`.  
  
3. **Test Dependency Handling:**  
   - Test dependencies are now detected and added only if `BUILD_TESTING` is enabled.  
   - They are added with `EXCLUDE_FROM_ALL`, and their tests and install targets are disabled.  
  
4. **Code Quality:**  
   - The code is now clearer, easier to follow, and more efficient.  
   - Reduces unnecessary CMake processing, speeding up configuration and avoiding spurious targets.  
  
---  
  
## Impact  
  
- **Performance:** Only the necessary libraries are present, so CMake processing will be much faster for partial builds.  
- **Clarity:** The build system more closely matches user expectations when `BOOST_INCLUDE_LIBRARIES` is set.  
- **Test Handling:** Test dependencies are handled explicitly, improving test-only builds and reducing noise.  
  
---  
  
## Suggestions & Comments  
  
- The changes are well written and the logic is sound.  
- The recursive dependency detection is robust and handles both normal and test dependencies.  
- The new approach avoids adding unnecessary libraries, which is a big win for users who only want a subset of Boost.  
  
**Minor suggestions:**  
- Consider adding or updating documentation/comments to describe the new logic for users and maintainers.  
- It might be helpful to add test cases (if possible) to ensure edge cases are handled, especially with circular dependencies or unusual library structures.  
- If there are CI jobs that set `BOOST_INCLUDE_LIBRARIES`, ensure they're updated or extended to cover the new logic.  
  
---  
  
## Recommendation  
  
**Approve** – This is a solid improvement to the Boost CMake superproject’s partial build handling, with clear benefits for users and maintainers.

---

## Comment 5

> Username: Flamefire  
> Created_at: 2025-09-11 07:29:55 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3278866146  

> Looks like we need to do a bit of special casing.  
  
Done. I also added a CI job that includes all libraries one-by-one and configures them only. This should ensure that we catch all such issues.

---

## Comment 6

> Username: pdimov  
> Created_at: 2025-09-11 09:23:56 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3279445893  

```  
CMake Error at libs/beast/test/beast/core/CMakeLists.txt:17 (target_link_libraries):  
  Target "boost_beast_tests_core" links to:  
  
    Boost::filesystem  
  
  but the target was not found.  Possible reasons include:  
  
    * There is a typo in the target name.  
    * A find_package call is missing for an IMPORTED target.  
    * An ALIAS target is missing.  
  
  
  
CMake Error at libs/beast/test/beast/http/CMakeLists.txt:17 (target_link_libraries):  
  Target "boost_beast_tests_http" links to:  
  
    Boost::filesystem  
  
  but the target was not found.  Possible reasons include:  
  
    * There is a typo in the target name.  
    * A find_package call is missing for an IMPORTED target.  
    * An ALIAS target is missing.  
  
  
  
-- Generating done (0.2s)  
CMake Generate step failed.  Build files cannot be regenerated correctly.  
```  
but the CI job succeeds.

---

## Comment 7

> Username: pdimov  
> Created_at: 2025-09-11 09:28:22 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3279484730  

There's also  
```  
CMake Error at libs/log/CMakeLists.txt:528 (target_link_libraries):  
  Target "boost_log" links to:  
  
    Boost::asio_core  
  
  but the target was not found.  Possible reasons include:  
  
    * There is a typo in the target name.  
    * A find_package call is missing for an IMPORTED target.  
    * An ALIAS target is missing.  
  
  
  
CMake Error at libs/log/CMakeLists.txt:655 (target_link_libraries):  
  Target "boost_log_setup" links to:  
  
    Boost::asio_core  
  
  but the target was not found.  Possible reasons include:  
  
    * There is a typo in the target name.  
    * A find_package call is missing for an IMPORTED target.  
    * An ALIAS target is missing.  
  
  
  
-- Generating done (0.2s)  
CMake Generate step failed.  Build files cannot be regenerated correctly.  
```  
but the Asio CMake is now broken (installation doesn't work) so this asio_core target may disappear.  
  
https://github.com/chriskohlhoff/asio/issues/1660  
  
Note that the suggested fix for the Asio issue will be broken by this patch.

---

## Comment 8

> Username: pdimov  
> Created_at: 2025-09-11 09:29:26 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3279491978  

```  
CMake Error at tools/cmake/include/BoostTest.cmake:175 (target_link_libraries):  
  Target "boost_math-ccmath_abs_incl_test" links to:  
  
    Boost::numeric_ublas  
  
  but the target was not found.  Possible reasons include:  
  
    * There is a typo in the target name.  
    * A find_package call is missing for an IMPORTED target.  
    * An ALIAS target is missing.  
  
Call Stack (most recent call first):  
  libs/math/test/compile_test/CMakeLists.txt:7 (boost_test)  
  
  
-- Generating done (0.2s)  
CMake Generate step failed.  Build files cannot be regenerated correctly.  
```

---

## Comment 9

> Username: pdimov  
> Created_at: 2025-09-11 09:30:43 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3279498234  

```  
CMake Warning at libs/msm/test/CMakeLists.txt:13 (find_package):  
  By not providing "Findboost_serialization.cmake" in CMAKE_MODULE_PATH this  
  project has asked CMake to find a package configuration file provided by  
  "boost_serialization", but CMake did not find one.  
  
  Could not find a package configuration file provided by  
  "boost_serialization" with any of the following names:  
  
    boost_serializationConfig.cmake  
    boost_serialization-config.cmake  
  
  Add the installation prefix of "boost_serialization" to CMAKE_PREFIX_PATH  
  or set "boost_serialization_DIR" to a directory containing one of the above  
  files.  If "boost_serialization" provides a separate development package or  
  SDK, be sure it has been installed.  
```

---

## Comment 10

> Username: pdimov  
> Created_at: 2025-09-11 09:32:48 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3279508732  

There are a few more.

---

## Comment 11

> Username: Flamefire  
> Created_at: 2025-09-11 10:53:46 UTC  
> Updated_at: 2025-09-11 11:02:46 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3279908726  

> but the CI job succeeds.  
  
Fixed. Extracted the job to #87   
  
> There are a few more.  
  
Fixed now  
  
>   
>   Could not find a package configuration file provided by  
>   "boost_serialization" with any of the following names:  
>   
>     boost_serializationConfig.cmake  
>     boost_serialization-config.cmake  
  
Unrelated [bug](https://github.com/boostorg/msm/pull/99) in MSM

---

## Comment 12

> Username: nigels-com  
> Created_at: 2025-09-11 12:29:15 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3280382539  

Looks good to me.  Works for cmake-3.8 in [boostorg/json#1106](https://github.com/boostorg/json/pull/1106)

---

## Comment 13

> Username: pdimov  
> Created_at: 2025-09-11 13:14:53 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3280609865  

I'm not convinced that the new scanner will not cause us issues, as it will pick up a lot more Boost::... things than the current one.  
  
What's the new logic?

---

## Comment 14

> Username: pdimov  
> Created_at: 2025-09-11 13:16:18 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3280617104  

> Unrelated bug in MSM  
  
The MSM CI looks rusty. Maybe I need to update it.

---

## Comment 15

> Username: Flamefire  
> Created_at: 2025-09-11 14:08:19 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3280957111  

> I'm not convinced that the new scanner will not cause us issues, as it will pick up a lot more Boost::... things than the current one.  
  
I think that should still be pretty safe as libraries picked up by the scanner get excluded from ALL and with `BUILD_TESTING` there can only be *less* libraries added as all were added before.  
  
> What's the new logic?  
  
Base case: Pick up anything that looks like a Boost library, i.e. `Boost::foo` supporting multiple in one line, e.g. `Boost::foo Boost::bar`     
However try to exclude commented blocks, i.e. don't pick lines where a `#` is in front of the library name and limit to the part of the line that has no `#`  
  
Determine 2 sets of dependencies:  
1. All direct & transitive dependencies based on the root CMLs  
2. If BUILD_TESTING grep for all CMLs in the test and example folders and their transitive dependencies  
  
The former are handled as before and the latter limits the libraries added when `BUILD_TESTING=ON`, i.e. instead of adding all Boost libraries only the required test dependencies are added.  
  
So we could rather not add enough libraries if the logic for the 2nd list was wrong, which is why I enhanced the CI. That found e.g. tests in multiple subfolders or example-folders

---

## Comment 16

> Username: pdimov  
> Created_at: 2025-09-11 15:24:33 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3281406013  

> I think that should still be pretty safe as libraries picked up by the scanner get excluded from ALL and with BUILD_TESTING there can only be less libraries added as all were added before.  
  
But we're modifying the scanner for the non-testing case as well, don't we?

---

## Comment 17

> Username: Flamefire  
> Created_at: 2025-09-11 16:24:45 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3281732326  

> > I think that should still be pretty safe as libraries picked up by the scanner get excluded from ALL and with BUILD_TESTING there can only be less libraries added as all were added before.  
>   
> But we're modifying the scanner for the non-testing case as well, don't we?  
  
A true, I thought they get excluded from ALL too.  
  
So yes it might pick up more stuff but I don't think anything it should not. Tested it:  
  
- Beast: `Boost::winapi)` missed  
- Cobald: Boost::container was missed even though it is optional  
- Process: Optional Boost::filesystem  
  
Besides that a few minor differences like picking up `Boost::headers` or the same library, e.g. for:  
```  
add_library(  
  Boost::foo  
 )  
```

---

## Comment 18

> Username: pdimov  
> Created_at: 2025-09-11 16:34:27 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3281779687  

We should figure out ways to manually give hints to the scanner (include this library, don't include that library.) At the moment that's easy - you just put or don't put the library on a line by itself.

---

## Comment 19

> Username: Flamefire  
> Created_at: 2025-09-11 17:01:49 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3281908499  

I think we only need the latter: Excluding.  
  
The current scanner should pick up all dependencies so I can't imagine a use case where one would need to add more.

---

## Comment 20

> Username: pdimov  
> Created_at: 2025-09-11 17:18:53 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3281970684  

One obvious example is Boost::asio_core, which can be fixed by the CML author by manually adding Boost::asio somewhere.  
  
You could of course do ```set(foo Boost::asio)```, but it might be better to have something more explicit (and side effects free.)

---

## Comment 21

> Username: Flamefire  
> Created_at: 2025-09-11 17:39:04 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3282028190  

> One obvious example is Boost::asio_core, which can be fixed by the CML author by manually adding Boost::asio somewhere.  
  
You mean by the consuming library? Yes that's possible, but I'd rather fix this here than in every library using it.

---

## Comment 22

> Username: pdimov  
> Created_at: 2025-09-11 18:05:57 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3282106651  

We'll need to "fix it here" for every such occurrence. It's much better if people can "fix it there" without having to rely on the central infrastructure each time.  
  
Note that asio_core was introduced two months ago.  
  
https://github.com/boostorg/asio/commit/be4af2d923f6145cd384e9bd1d572a5f204f5461

---

## Comment 23

> Username: Flamefire  
> Created_at: 2025-09-11 18:09:12 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3282115653  

> Note that asio_core was introduced two months ago.  
  
And nobody noticed  
  
 > We'll need to "fix it here" for every such occurrence. It's much better if people can "fix it there" without having to rely on the central infrastructure each time.  
  
But we need to fix it here either way as the parser would/could have trouble too: https://github.com/boostorg/asio/blob/be4af2d923f6145cd384e9bd1d572a5f204f5461/CMakeLists.txt#L38

---

## Comment 24

> Username: pdimov  
> Created_at: 2025-09-11 18:15:59 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3282137004  

I'm pretty sure the parser didn't have trouble with it.  
  
> And nobody noticed  
  
People did notice post-release because this broke Asio CMake installation.

---

## Comment 25

> Username: Flamefire  
> Created_at: 2025-09-11 18:44:52 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3282221468  

> I'm pretty sure the parser didn't have trouble with it.  
  
Ah true, the check was basically `if(current_lib == "asio_core") add_subdir(asio)` so it just did never match which didn't hurt.  
  
Then maybe linter-like macros/comments like this?  
```  
# Boost-Include Boost::Add  
# Boost-Exclude Boost::Sub  
```

---

## Comment 26

> Username: pdimov  
> Created_at: 2025-09-11 18:48:28 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3282234114  

Yes, this should work.

---

## Comment 27

> Username: Flamefire  
> Created_at: 2025-09-12 17:22:05 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3286243475  

Ok, I implemented this. It now supports comments in CMLs like:  
```  
  # Boost-Include: Boost::add_this  
  #   Boost-Exclude   Boost::remove_this  
```  
  
Whitespace is ignored and the colon is optional

---

## Comment 28

> Username: pdimov  
> Created_at: 2025-09-12 18:34:30 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3286430125  

Do you consider this ready to merge?

---

## Comment 29

> Username: Flamefire  
> Created_at: 2025-09-12 18:49:06 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3286471933  

> Do you consider this ready to merge?  
  
Yes.  
  
We should document that include/exclude part somewhere but I haven't seen where it fits best. The main README seems to be for users not Boost authors

---

## Comment 30

> Username: pdimov  
> Created_at: 2025-09-12 18:51:21 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3286477653  

The documentation for authors is https://github.com/boostorg/cmake/blob/develop/developer.md but I still haven't finished it.

---

## Comment 31

> Username: nigels-com  
> Created_at: 2025-09-14 06:15:01 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3289256722  

I'm going away on vacation next week.  I probably won't be paying such close attention to boost and cmake, but I wanted to thank you for the engagement and work on this.  I think that having cmake working is a bit of an enabler for encouraging more boost engagement, so it was this curiosity that was driving this exploration of boost and cmake.  Thanks all.  (I've decided _not_ to take a laptop on vacation, which feels wrong, but I'll see how I go...)

---

## Comment 32

> Username: Flamefire  
> Created_at: 2025-09-14 11:33:50 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3289469681  

> The documentation for authors is https://github.com/boostorg/cmake/blob/develop/developer.md but I still haven't finished it.  
  
I added a commit to explain the pragmas. With that this can be merged.

---

## Comment 33

> Username: pdimov  
> Created_at: 2025-09-14 23:24:57 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3290024612  

Instead of just rewriting, you should explain that while the exact form of `target_link_libraries` was necessary until Boost 1.89 for reasons, it's no longer needed for 1.90 and later because this and that.

---

## Comment 34

> Username: Flamefire  
> Created_at: 2025-09-15 07:23:03 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3290813077  

When this is merged that old form is no longer relevant so I think it only confuses to put/keep that information especially as there is no change to existing code required. So I don't see what that extra piece of information provides.  
  
If you still want a reference to the Boost version would you be ok with:  
  
> Note that the exact form of the directive, with each `Boost::libname` target on  
its own line, is no longer necessary after Boost 1.89.  
  
That would be short enough to answer a potential questions like "Why did I had to do that extra work before?"

---

## Comment 35

> Username: pdimov  
> Created_at: 2025-09-18 00:07:35 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3304906523  

> If you still want a reference to the Boost version would you be ok with:  
  
Yes please.

---

## Comment 36

> Username: pdimov  
> Created_at: 2025-09-18 16:11:15 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3308347356  

OK, let's have a go and see what happens.

---

## Comment 37

> Username: ashtum  
> Created_at: 2025-09-21 14:52:53 UTC  
> Updated_at: 2025-09-21 18:29:24 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3316045099  

This broke several libraries we are working on because the dependent libraries are linked in `test/unit/CMakeLists.txt` instead of `test/CMakeLists.txt`.   
For example:  
https://github.com/cppalliance/buffers/blob/7312c250dcb9d3b7264c3afae804843557f5691c/test/unit/CMakeLists.txt#L39-L43  
  
This happens even though we add test dependencies to BOOST_INCLUDE_LIBRARIES in the root CML:  
https://github.com/cppalliance/buffers/blob/7312c250dcb9d3b7264c3afae804843557f5691c/CMakeLists.txt#L67-L73  
  
**Edit:** It looks like dependencies in subdirectories are also getting picked up, we just needed to use Boost namespaced targets.

---

## Comment 38

> Username: Flamefire  
> Created_at: 2025-09-22 07:30:19 UTC  
> Url: https://github.com/boostorg/cmake/pull/86#issuecomment-3317349790  

> **Edit:** It looks like dependencies in subdirectories are also getting picked up, we just needed to use Boost namespaced targets.  
  
I tested a few examples and found that some libraries did use `test/foo/CML` so I enhanced the glob to account for that, even if that is possibly picking up to much.  
  
Using the Boost namespaced targets should be a given as I can't see an advantage of not doing so.     
Advantages of the namespaced ones:  
- Clear CMake error when the library does not exist (instead of linker error later on)  
- Works also with `find_package` using an installed Boost.  
- No reliance on internal targets  
  
> the superproject cannot resolve dependencies from subdirectory CMakeLists:  
  
Side note to be clear: "cannot resolve dependencies [from any CMakeLists]"  
  
If you find any other issues with the new scanner please report.

---
