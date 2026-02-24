# #3 - BoostConfig.cmake does not honor QUIET argument [Closed]

> Username: dennisklein  
> Created at: 2019-04-15 18:56:22 UTC  
> Updated at: 2019-04-19 12:40:24 UTC  
> Closed at: 2019-04-19 12:40:24 UTC  
> Url: https://github.com/boostorg/boost_install/issues/3  

Dear Boost team,  
  
When integrating a Boost `1.70.0` installation in our CMake based projects, we get spammed like this:  
  
```  
-- Found Boost 1.70.0 at /home/dklein/boost_1_70_0_install/lib/cmake/Boost-1.70.0  
--   Requested configuration: QUIET REQUIRED COMPONENTS system;program_options;container  
-- Found boost_headers 1.70.0 at /home/dklein/boost_1_70_0_install/lib/cmake/boost_headers-1.70.0  
-- Found boost_system 1.70.0 at /home/dklein/boost_1_70_0_install/lib/cmake/boost_system-1.70.0  
--   libboost_system.a  
-- Adding boost_system dependencies: headers  
-- Found boost_program_options 1.70.0 at /home/dklein/boost_1_70_0_install/lib/cmake/boost_program_options-1.70.0  
--   libboost_program_options.a  
-- Adding boost_program_options dependencies: headers  
-- Found boost_container 1.70.0 at /home/dklein/boost_1_70_0_install/lib/cmake/boost_container-1.70.0  
--   libboost_container.a  
-- Adding boost_container dependencies: headers  
-- Boost 1.70.0 found.  
-- Found Boost components:  
   system;program_options;container  
```  
  
even though we passed `QUIET` to the `find_package` command as the output confirms.  
  
Looking at the information printed, it reminds on the output of the standard handler CMake provides for FindModule authors, see [find_package_handle_standard_args()](https://cmake.org/cmake/help/v3.14/module/FindPackageHandleStandardArgs.html). If you really need to print the above information, I recommend (with decending order of importance)  
  
  1. to only print anything, if `QUIET` is **not** passed,  
  2. only print information **once**, reprint only, if something has changed (this is also the behaviour of the [above mentioned package](https://cmake.org/cmake/help/v3.14/module/FindPackageHandleStandardArgs.html)), and  
  3. streamline the user experience by following the output formatting chosen in the [above mentioned package](https://cmake.org/cmake/help/v3.14/module/FindPackageHandleStandardArgs.html) (Even the upstream `FindBoost.cmake` shipped with CMake delivers a messy user experience, because it insists in printing status lines in its own custom format which makes CMake output hard to read in the context of many dependencies that all insist on their own style...)  
  
(Instead of printing anything at all in any case, I would prefer if you just export properly documented CMake variables that contain all of such useful infos and enable the user of your package to decide, if anything needs to be printed or evaluated in any other manner. At least it would be nice, if a complete and reasonably detailed variable export of useful infos would get a prioritized attention opposed to the printed human-readable output.)  
  
Thank you.

---

## Comment 1

> Username: dennisklein  
> Created at: 2019-04-15 19:02:23 UTC  
> Updated at: 2019-04-15 19:02:47 UTC  
> Url: https://github.com/boostorg/boost_install/issues/3#issuecomment-483377001  

If you agree with (some of?) my points, I can offer to spend some time on it and propose them in the form of a pull request.

---

## Comment 2

> Username: pdimov  
> Created at: 2019-04-16 16:27:20 UTC  
> Url: https://github.com/boostorg/boost_install/issues/3#issuecomment-483734287  

You are correct that at present the config files talk too much. The reason I made and kept them this way is that the support is new and the verbosity helps identify issues. It should certainly be toned down for the next release if we determine that things work more or less correctly 99% of the time.  
  
To be more specific, the main reason to keep this verbosity on by default is `FindBoost.cmake`'s behavior of finding and delegating to `BoostConfig.cmake`. This has never been an issue so far because such config file didn't exist. Now however, when Boost 1.70 installs one, many people will find, and are finding, that they suddenly started using the configuration file from Boost 1.70, without having changed anything.  
  
This is a surprising change in behavior for them, and the verbosity helps somewhat.  
  
In addition, `FindBoost.cmake` even invokes `BoostConfig` with `QUIET`. So I deliberately ignore `QUIET` as it generally doesn't come from the user.  
  
All that said, I agree that in the next release `QUIET` should be respected, and the messages should follow a more idiomatic style. I'll probably keep the current ones behind `Boost_VERBOSE`, off by default.

---

## Comment 3

> Username: dennisklein  
> Created at: 2019-04-16 19:53:47 UTC  
> Url: https://github.com/boostorg/boost_install/issues/3#issuecomment-483819267  

Thank you for your clear answer. With the prospect that `QUIET` will be honored in a future release I consider this issue as resolved from my side. I leave it to your discretion to close it.

---

## Comment 4

> Username: dennisklein  
> Created at: 2019-04-18 14:53:54 UTC  
> Url: https://github.com/boostorg/boost_install/issues/3#issuecomment-484543725  

For reference: https://gitlab.kitware.com/cmake/cmake/issues/19186

---

## Comment 5

> Username: pdimov  
> Created at: 2019-04-19 12:40:24 UTC  
> Url: https://github.com/boostorg/boost_install/issues/3#issuecomment-484883058  

Should be fixed in https://github.com/boostorg/boost_install/commit/4bf56cc76fe2291a999d51e2f62d348c31e5ae4a. Also added FPHSA-compatible variables in https://github.com/boostorg/boost_install/commit/05ed67bf56412999c3c0a6d8ed02cb4fb74531dc.
