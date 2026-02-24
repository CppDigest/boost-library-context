# #89 Install extra/ directory, and configure .natvis files on Windows [Merged]

> Username: k3DW  
> Created at: 2025-09-11 21:40:47 UTC  
> Updated at: 2025-10-19 07:30:06 UTC  
> Merged at: 2025-10-18 13:07:05 UTC  
> Closed at: 2025-10-18 13:07:05 UTC  
> Url: https://github.com/boostorg/cmake/pull/89  

Follow-up of #81

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2025-09-13 18:45:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/cmake/pull/89#pullrequestreview-3221245643  

📁 include/BoostInstall.cmake

```diff
 573 |     install(DIRECTORY "${__HEADER_DIRECTORY}/" DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}")
 574 | 
 575 |+     if(EXISTS "${__HEADER_DIRECTORY}/../extra")
```

> Username: pdimov  
> Created_at: 2025-09-13 18:45:19 UTC  
> Updated_at: 2025-09-13 18:45:20 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#discussion_r2346893827  

I don't think EXTRA_DIRECTORY should be derived from HEADER_DIRECTORY. These two can in principle be entirely separate. It doesn't make sense for `boost_install_target` to take the extra parameter but for `boost_install` not to.

> Username: k3DW  
> Created_at: 2025-09-13 19:25:08 UTC  
> Updated_at: 2025-09-13 19:25:09 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#discussion_r2346909628  

What do you think about adding `EXTRA_DIRECTORY` as another argument, but looking for `"${__HEADER_DIRECTORY}/../extra"` if `EXTRA_DIRECTORY` is not specified?

> Username: k3DW  
> Created_at: 2025-10-05 19:56:54 UTC  
> Updated_at: 2025-10-05 19:56:55 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#discussion_r2404640914  

I updated the PR without any searching for a default extra directory. It can only be provided.


---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2025-09-13 18:49:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/cmake/pull/89#pullrequestreview-3221248540  

📁 include/BoostInstall.cmake

```diff
 227 |+     get_filename_component(libname "${libname}" NAME)
 228 |+ 
 229 |+     foreach(file IN LISTS files)
```

> Username: pdimov  
> Created_at: 2025-09-13 18:49:20 UTC  
> Updated_at: 2025-09-13 18:49:21 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#discussion_r2346895302  

I'm not sure this is correct. It seems to me that, in case `boost_install` is invoked with more than one target, this will attach all .natvis files as sources to all targets. But this isn't likely to be correct. (Or maybe it will be "correct" in that it will work, but unintended.)  
  
It _probably_ makes more sense for `boost_foo.natvis` to be attached to the `boost_foo` target and for `boost_bar.natvis` to be attached to the `boost_bar` target.

> Username: k3DW  
> Created_at: 2025-10-05 19:59:15 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#discussion_r2404641783  

I updated the PR so now it will only attach natvis files to a target that were already previously attached to that target. There is no constraint on the naming scheme of the natvis file though, as long as it ends in ".natvis" and resides in the extra directory.


---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2025-09-13 18:55:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/cmake/pull/89#pullrequestreview-3221254459  

📁 include/BoostInstall.cmake

```diff
 231 |+       get_filename_component(file "${file}" NAME)
 232 |+ 
 233 |+       target_sources("${lib}" INTERFACE
```

> Username: pdimov  
> Created_at: 2025-09-13 18:55:48 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#discussion_r2346897701  

Actually, now that I think about it, this is completely incorrect. This function should not be _adding_ sources to `lib`. It should instead look at the existing sources and, if some of them are .natvis files in the extra directory, should update the property to the correct BUILD_INTERFACE and INSTALL_INTERFACE values, like the include directory is updated.

> Username: k3DW  
> Created_at: 2025-09-13 19:32:46 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#discussion_r2346914918  

I'll have to look into this more later. I thought it was more idiomatic to call `target_sources()` with `INTERFACE`, instead of adding to the `INTERFACE_SOURCES` property directly.  
  
The issue I found was, the list of `INTERFACE_SOURCES` only contains exactly the text that was given to `target_sources()` or `add_executable/add_library()`, not the full path, so it may be difficult to determine which sources are in the extra directory. If I loop over the list of `INTERFACE_SOURCES` instead of using the file glob, I think it should be done without any reference to the extra directory, and instead work on all .natvis sources that belong to the target.

> Username: k3DW  
> Created_at: 2025-10-05 19:59:43 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#discussion_r2404641918  

I was wrong about my comment above, I looped over the `INTERFACE_SOURCES` while still using the extra directory.


---

## Comment 4

> Username: pdimov  
> Created_at: 2025-10-06 00:42:30 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#issuecomment-3369582297  

Let's leave BOOST_INSTALL_INCLUDE_SUBDIR alone and (always, regardless of layout) install into CMAKE_INSTALL_DATADIR/boost-1.90.0.  
  
I'll take a look at the rest later.

---

## Comment 5

> Username: k3DW  
> Created_at: 2025-10-06 01:50:30 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#issuecomment-3369664672  

It makes more sense to have matching installation for `include/` and `extra/`. Using `BOOST_INSTALL_INCLUDE_SUBDIR` avoids that issue. For example, either we get `include/boost-1_90` and `share/boost-1_90`, or we get simply `include` and `share`. I think they should always match.

---

## Comment 6

> Username: pdimov  
> Created_at: 2025-10-06 17:26:22 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#issuecomment-3372971179  

No, I don't think it does. The include subdir is maintained for consistency with `b2 install`, new things (e.g. the CMake config files) use the -1.90.0 convention.  
  
I don't think that dumping all the `libs/libname/extra` directories into `/usr/share` is what we want (or what the user would have wanted.) We can afford to do this for `libs/libname/include` only because their contents always have a `boost` subdirectory, usually `boost/libname` subdirectory, and collisions are checked by `b2 headers`. We don't have any established conventions for the contents of `extra`, or any enforcement or checks for those nonexistent conventions.  
  
So to avoid dumping random things into `/usr/share`, we should install in a subdirectory, and to avoid libraries stepping over one another's toes, we probably should install into `boost_libname-1.90.0`, like the CMake configs.

---

## Comment 7

> Username: k3DW  
> Created_at: 2025-10-06 22:21:11 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#issuecomment-3374464184  

Understood, I can make that change, and remove the configurability from it.

---

## Review 8 [Commented]

> Username: pdimov  
> Created_at: 2025-10-07 16:45:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/cmake/pull/89#pullrequestreview-3310983919  

📁 include/BoostInstall.cmake

```diff
 237 |+ 
 238 |+         # Remove this .natvis file from the INTERFACE_SOURCES target property,
 239 |+         # only if it appears as a direct path, not a generator expression.
```

> Username: pdimov  
> Created_at: 2025-10-07 16:45:47 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#discussion_r2411252003  

Why do we remove the .natvis file only when it's not a generator expression? We add it unconditionally below.

> Username: k3DW  
> Created_at: 2025-10-08 02:35:49 UTC  
> Updated_at: 2025-10-08 03:02:36 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#discussion_r2412356360  

I'll make it more explicit in the actual logic. There are 2 possible scenarios here  
  
1. The source file is `"${extradir}/${natvis_file}"`. If that's the case, remove it from the source list, and add the proper generator expressions to the source list.  
2. The source file is `"$<BUILD_INTERFACE:${extradir}/${natvis_file}>"`. If that's the case, then the removal is a no-op, and one of the generator expressions is repeated in the source list when they are added to the source list.  
  
Either way, we end up with the `BUILD_INTERFACE` and `INSTALL_INTERFACE` generator expressions for the source file, and we remove any mention of this source file without those generator expressions.

> Username: pdimov  
> Created_at: 2025-10-08 09:56:12 UTC  
> Updated_at: 2025-10-08 09:56:13 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#discussion_r2413282929  

Wouldn't the `BUILD_INTERFACE` be duplicated if we don't remove it first, then add it? Or does `target_sources` deduplicate?  
  
There's also a case where the source list already contains both `BUILD_INTERFACE` and `INSTALL_INTERFACE` for our .natvis. It's not clear whether we should care about it.  
  
Maybe we should just not handle the `BUILD_INTERFACE` case at all, and assume that if the user has that in the list, then he's responsible for the `INSTALL_INTERFACE` part as well.

> Username: k3DW  
> Created_at: 2025-10-08 20:07:29 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#discussion_r2414909853  

Sure, I can do that. I'll only handle the non-generator expression case, and leave `BUILD_INTERFACE` alone.


---

## Review 9 [Commented]

> Username: pdimov  
> Created_at: 2025-10-07 16:47:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/cmake/pull/89#pullrequestreview-3310990527  

📁 include/BoostInstall.cmake

```diff
 593 |+     get_filename_component(libname "${__EXTRA_DIRECTORY}" DIRECTORY)
 594 |+     get_filename_component(libname "${libname}" NAME)
 595 |+     string(REGEX REPLACE "\\/boost\\-" "/boost_${libname}-" extra_subdir "${__boost_default_include_subdir}")
```

> Username: pdimov  
> Created_at: 2025-10-07 16:47:52 UTC  
> Updated_at: 2025-10-07 16:47:53 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#discussion_r2411256715  

I don't understand this logic. What is it supposed to do?

> Username: k3DW  
> Created_at: 2025-10-08 02:53:40 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#discussion_r2412374946  

I need some way of grabbing the library name. If `"${__EXTRA_DIRECTORY}"` evaluates to `"path/boost/libs/unordered/extra"`, this code grabs "unordered". Then it changes "/boost-1_90" to "/boost_unordered-1_90" by using `__boost_default_include_subdir`.  
  
Is there an easier way to grab the lib name? The CMake configs do their install on a per-target basis, so the subdirectories are based on the target names. Installing the extra directory should be more akin to installing the include directory, happening only once per call to `boost_install`, not for each target.

> Username: pdimov  
> Created_at: 2025-10-08 09:52:54 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#discussion_r2413274072  

You shouldn't be using the include subdir. Use the version.  
  
Obtaining the subdirectory name looks fine, although instead of "boost_libdir-1.90.0" it'd probably be better if we use "boost-1.90.0/libdir".  
  
"boost-libdir-1.90.0" is technically more correct, because the version could theoretically be (incorrectly?) set by the library to something other than the Boost release version, but "boost-1.90.0/libdir" is prettier because the subdirectory name is used as a subdirectory name, and fewer items are placed into /usr/share. Hard to pick between these two; let's go with the latter for now.

> Username: k3DW  
> Created_at: 2025-10-08 20:08:09 UTC  
> Updated_at: 2025-10-08 20:08:10 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#discussion_r2414911166  

Ah right, because `__boost_default_include_subdir` may not always contain the verison. Understood, I'll make this change.


---

## Review 10 [Commented]

> Username: pdimov  
> Created_at: 2025-10-09 01:01:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/cmake/pull/89#pullrequestreview-3316914770  

📁 include/BoostInstall.cmake

```diff
 230 |+ 
 231 |+     # Look for .natvis files in the given extra directory
 232 |+     if(src MATCHES "\\${extradir}\\/(.*)\\.natvis")
```

> Username: pdimov  
> Created_at: 2025-10-09 01:01:15 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#discussion_r2415321542  

I'm not sure I understand the leading `\\` in the regexp here; but it probably doesn't matter because since we're only checking for the exact `${extradir}/something.natvis` now, we shouldn't be using a regexp, but path manipulation functions. (I'm not sure that the regex was correct to begin with because `${extradir}` could easily have contained special characters such as dot.)

> Username: k3DW  
> Created_at: 2025-10-09 03:53:22 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#discussion_r2415501368  

The leading `\\` was for the `$`. But you're right, it must evaluate the variable before the regex, so it's an error. I'll change it to use filename components.


---

## Review 11 [Commented]

> Username: pdimov  
> Created_at: 2025-10-09 01:05:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/cmake/pull/89#pullrequestreview-3316920293  

📁 include/BoostInstall.cmake

```diff
 241 |+ 
 242 |+         # Add this .natvis file to the INTERFACE_SOURCES target property, prefixed properly.
 243 |+         target_sources("${lib}" INTERFACE $<BUILD_INTERFACE:${extradir}/${natvis_file}> $<INSTALL_INTERFACE:${CMAKE_INSTALL_DATADIR}/${natvis_file}>)
```

> Username: pdimov  
> Created_at: 2025-10-09 01:05:49 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#discussion_r2415325969  

`${CMAKE_INSTALL_DATADIR}/${natvis_file}` is no longer correct, because we're installing in a subdirectory of `CMAKE_INSTALL_DATADIR` now. This function should probably take `extrainstalldir` in addition to `extradir`. Of course it would need to obtain that from somewhere; we need to either duplicate the logic of deriving `extrainstalldir` in `boost_install_target`, or pass it as a parameter.  
  
Passing it as a parameter is probably better because of an additional subtlety. The installation of `extradir` in `boost_install` can be skipped when `BOOST_SKIP_INSTALL_RULES` or `CMAKE_SKIP_INSTALL_RULES` is set. If this occurs, we probably shouldn't add the INSTALL_INTERFACE part here, because we aren't installing the .natvis file.

> Username: k3DW  
> Created_at: 2025-10-09 04:09:22 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#discussion_r2415517901  

The calling function `boost_install_target` already checks both `BOOST_SKIP_INSTALL_RULES` and `CMAKE_SKIP_INSTALL_RULES`, and returns early if either one is set. So I think it's safe to keep `INSTALL_INTERFACE` here.

> Username: pdimov  
> Created_at: 2025-10-09 11:25:56 UTC  
> Updated_at: 2025-10-09 11:25:57 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#discussion_r2416463659  

oh, OK.


---

## Comment 12

> Username: pdimov  
> Created_at: 2025-10-10 14:38:37 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#issuecomment-3390525649  

You have legitimate CI failures if you haven't noticed:  
```  
-- Adding Boost library assert  
CMake Error at /Users/runner/work/cmake/boost-root/tools/cmake/include/BoostInstall.cmake:273 (message):  
  boost_install_target: both or neither of EXTRA_DIRECTORY and  
  EXTRA_INSTALL_DIRECTORY must be given.  
Call Stack (most recent call first):  
  /Users/runner/work/cmake/boost-root/tools/cmake/include/BoostInstall.cmake:606 (boost_install_target)  
  /Users/runner/work/cmake/boost-root/tools/cmake/include/BoostRoot.cmake:202 (boost_install)  
  /Users/runner/work/cmake/boost-root/tools/cmake/include/BoostRoot.cmake:421 (__boost_auto_install)  
  /Users/runner/work/cmake/boost-root/CMakeLists.txt:20 (include)  
```  
That's probably because when e.g. BOOST_SKIP_INSTALL_RULES is set, EXTRA_INSTALL_DIRECTORY is empty, and your check in boost_install_target precedes the check for BOOST_SKIP_INSTALL_RULES (because boost_install_target does more than just install, and those other things aren't skipped when BOOST_SKIP_INSTALL_RULES is set.)

---

## Comment 13

> Username: pdimov  
> Created_at: 2025-10-11 15:02:44 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#issuecomment-3393404879  

Thank you Braden for your work on this. I think this is ready to merge and will do so a bit later.

---

## Comment 14

> Username: k3DW  
> Created_at: 2025-10-11 16:22:00 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#issuecomment-3393472117  

Great thank you

---

## Comment 15

> Username: pdimov  
> Created_at: 2025-10-18 14:12:33 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#issuecomment-3418460785  

I'm playing with this, and I notice that the installed Assert doesn't have the .natvis file - because it's added as a PRIVATE source. Fair enough. When I make it PUBLIC, it appears in the imported target's sources, and the path to it seems correct.  
  
But this gets me thinking. I also add the headers to Boost::assert, also as PRIVATE. So you get them in the project if you use `add_subdirectory` (or generate and open Boost.sln), but you don't when Boost is installed. Supposing one would like to make the headers appear in the installed project as well, one would add them as PUBLIC sources too. But then, their paths will not be adjusted because we only do this for things in `extra/` (and only when their extension is `.natvis`, which seems a bit unprincipled.)  
  
Maybe we should also apply the same treatment to (a) sources in `include/` and (b) sources in `extra/` that aren't `.natvis`.  
  
(Keeping the scope of this patch limited to `extra/**/*.natvis` was correct; I'm saying that we should consider expanding it, maybe for the release after the next.)

---

## Comment 16

> Username: pdimov  
> Created_at: 2025-10-18 19:35:14 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#issuecomment-3418761627  

I also wonder why I'm checking for CMake 3.19 here  
  
https://github.com/boostorg/assert/blob/5dcb2af5213ae132b7531e45e7f93258cc33ffd8/CMakeLists.txt#L20  
  
Perhaps the CMake documentation was wrong when I looked at it, because now neither `target_sources` on an imported target, nor `source_group(TREE` seem to need 3.19.  
  
Also, given that the original issue that prompted all this wants `vcpkg` to install the .natvis files, I wonder whether the check for the Visual Studio generator isn't too conservative, because `vcpkg` uses Ninja.

---

## Comment 17

> Username: k3DW  
> Created_at: 2025-10-18 22:59:07 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#issuecomment-3419037467  

> Maybe we should also apply the same treatment to (a) sources in `include/` and (b) sources in `extra/` that aren't `.natvis`.  
  
I think I agree with that, depending on whether the installed headers would be transitively added to any VS projects that link to the installed Boost library. Definitely worth a discussion, and looking into further.  
  
> I also wonder why I'm checking for CMake 3.19 here  
  
I was wondering that a while back too, and I assumed that it was for `source_group`. From [the docs](https://cmake.org/cmake/help/latest/command/source_group.html):  
  
`Added in version 3.18: Allow using forward slashes (/) to specify subgroups.`  
  
> Also, given that the original issue that prompted all this wants vcpkg to install the .natvis files, I wonder whether the check for the Visual Studio generator isn't too conservative, because vcpkg uses Ninja.  
  
I think it will work as-is. I don't see the check for the VS generator, I see a check for MSVC [here](https://github.com/boostorg/cmake/blob/94a526fa210670f288fec19a632941cd4d9e3652/include/BoostInstall.cmake#L351-L363). That should work regardless of generator.

---

## Comment 18

> Username: pdimov  
> Created_at: 2025-10-18 23:51:12 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#issuecomment-3419069942  

https://github.com/boostorg/unordered/blob/e01b7b52a6ac1ea462aa470a52cb08a879903ff7/CMakeLists.txt#L26

---

## Comment 19

> Username: k3DW  
> Created_at: 2025-10-19 04:25:19 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#issuecomment-3419203220  

Got it. Then yes, I agree that this check is too conservative, and it should probably check for `MSVC` instead. As far as I know, the Visual Studio debugger can be used on all MSVC binaries, not just ones compiled with MSBuild. So then the Nativs files should included in all cases of MSVC. I'll make a suggested edit on my related Unordered PR.

---

## Comment 20

> Username: pdimov  
> Created_at: 2025-10-19 07:22:33 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#issuecomment-3419282427  

I am experimenting with adding the sources unconditionally for Assert, to see what would happen.  
  
I had to change the `boost_install` logic to not be limited to `if(MSVC)`, because if INTERFACE_SOURCES contains a source file rooted in `extra/`, this source file needs the adjustment regardless of whether `MSVC` is defined, otherwise there's an error that it's being installed while rooted in the source directory:  
  
https://github.com/boostorg/assert/actions/runs/18620181915/job/53090281688  
```  
CMake Error in libs/assert/CMakeLists.txt:  
  Target "boost_assert" INTERFACE_SOURCES property contains path:  
  
    "/home/runner/work/assert/boost-root/libs/assert/extra/boost_assert.natvis"  
  
  which is prefixed in the source directory.  
```  
  
So I did:  
  
https://github.com/boostorg/cmake/commit/466ba5e42ee83b803ecd3ef2f223e6405e458162  
  
Incidentally, this is still not wide enough, as it only works for sources immediately in `extra/` but not a subdirectory of `extra/`. So far we don't have any, but we might, in the future. If you're willing to work on that, we still have time before the feature freeze.  
  
And now (thanks to Alexander Grund's CI improvements) I have the answer to why I wanted CMake 3.19 in the check:  
  
https://github.com/boostorg/cmake/actions/runs/18626891304/job/53106142673  
```  
CMake Error at libs/assert/CMakeLists.txt:24 (target_sources):  
  target_sources may only set INTERFACE properties on INTERFACE targets  
  
  
CMake Error at libs/assert/CMakeLists.txt:27 (target_sources):  
  target_sources may only set INTERFACE properties on INTERFACE targets  
```  
  
I wonder what's the right thing here, switch to INTERFACE sources, or add back the version check (with a comment that explains why it's there :-)).

---

## Comment 21

> Username: pdimov  
> Created_at: 2025-10-19 07:30:06 UTC  
> Url: https://github.com/boostorg/cmake/pull/89#issuecomment-3419285990  

Oh, but using INTERFACE sources for the header files will require that we fixup them in `boost_install` as well. So I'll add back the version check, for now.

---
