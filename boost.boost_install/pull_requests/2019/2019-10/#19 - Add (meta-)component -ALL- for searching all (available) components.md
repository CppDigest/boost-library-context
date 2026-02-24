# #19 Add (meta-)component "ALL" for searching all (available) components. [Merged]

> Username: DenizThatMenace  
> Created at: 2019-10-28 16:23:19 UTC  
> Updated at: 2020-01-07 19:12:25 UTC  
> Merged at: 2020-01-07 19:12:24 UTC  
> Closed at: 2020-01-07 19:12:24 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19  

An implementation for the feature described in #16   
  
It allows to find all (associated) components, if `BoostConfig.cmake` was found.  
  
The usage would be  
```cmake  
find_package(Boost 1.72.0 COMPONENTS all)  
```  
or  
```cmake  
find_package(Boost 1.72.0 OPTIONAL_COMPONENTS all)  
```  
---  
  
fixes: #16

---

## Comment 1

> Username: pdimov  
> Created_at: 2019-10-28 16:48:14 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547039369  

What's the use case for `Boost_ALL_TARGETS`?

---

## Comment 2

> Username: DenizThatMenace  
> Created_at: 2019-10-28 17:01:11 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547045568  

One of the use-cases is still the same as described in #16:  
  
*To allow to comfortably modify all targets (or to even just know which targets were created).*  
  
For example, if you are calling `find_package(Boost 1.72.0 COMPONENTS all)` from a `CMakeLists.txt` in a subdirectory but you want to make these targets globally available (so that they can be used from within `CMakeLists.txt` files in above and/or parallel (sub)directories), then you could just do the following:  
  
```cmake  
find_package( Boost 1.72.0 COMPONENTS all )  
if (DEFINED Boost_ALL_TARGETS)  
    set_target_properties( ${Boost_ALL_TARGETS} PROPERTIES IMPORTED_GLOBAL true )  
endif ()  
```  
  
Other valid use cases exist as well, I assume, but that is the most prominent one for me, that always comes to mind.    
(As a side-note: Making Boost targets globally available was my original use case to implement and provide the `IMPORTED_GLOBAL` feature to CMake itself.)

---

## Review 3 [Commented]

> Username: DenizThatMenace  
> Created_at: 2019-10-28 17:17:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boost_install/pull/19#pullrequestreview-308014566  

📁 BoostConfig.cmake

```diff
 209 |+ 
 210 |+ 
 211 |+   #  list(REMOVE_DUPLICATES Boost_ALL_TARGETS)
```

> Username: DenizThatMenace  
> Created_at: 2019-10-28 17:17:24 UTC  
> Updated_at: 2019-11-05 16:23:23 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#discussion_r339689741  

I just realized, that these lines could be removed.    
I will do so before this pull-request will be accepted/merged.


---

## Comment 4

> Username: pdimov  
> Created_at: 2019-10-28 17:31:35 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547059960  

Thanks, I was wondering why we need the compatibility targets there (as they do nothing and aren't needed.)  
  
It seems to me that if you use the `all` feature you're definitely using BoostConfig as FindBoost doesn't support it, so there's no need to bother with old FindBoost compatibility targets.

---

## Comment 5

> Username: DenizThatMenace  
> Created_at: 2019-10-28 17:48:13 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547067323  

> It seems to me that if you use the `all` feature you're definitely using BoostConfig as FindBoost doesn't support it, so there's no need to bother with old FindBoost compatibility targets.  
  
There you have a point.    
I will change the pull request but wait for for the checks to finish running, first.  
  
Just to double check: The `Boost::dynamic_linking` has no meaning?    
From the documentation in `FindBoost.cmake` it does not seem to have anything to do with *automatic linking* but the comment before it in `BoostConfig.cmake` gives the impression that it does.

---

## Comment 6

> Username: pdimov  
> Created_at: 2019-10-28 17:53:27 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547069581  

With FindBoost, `Boost::dynamic_linking` adds `-DBOOST_ALL_DYN_LINK`. It does nothing with BoostConfig; `BOOST_*_DYN_LINK` is defined by the config files as appropriate.

---

## Comment 7

> Username: pdimov  
> Created_at: 2019-10-28 17:54:32 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547070023  

Is there some sort of CMake convention for such "virtual" components? `all` seems too ordinary, and a potential conflict with a hypothetical Boost.All library (if unlikely.)  
  
Maybe something like `__all__`?

---

## Comment 8

> Username: DenizThatMenace  
> Created_at: 2019-10-28 18:01:46 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547073155  

> Is there some sort of CMake convention for such "virtual" components? `all` seems too ordinary, and a potential conflict with a hypothetical Boost.All library (if unlikely.)  
>   
> Maybe something like `__all__`?  
  
That is something I do no know.    
Actually, I do not know if any "virtual" components exist in any other module that comes with CMake.  
  
Maybe, we should bring @bradking into the loop, here?

---

## Comment 9

> Username: bradking  
> Created_at: 2019-10-28 18:13:12 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547078014  

CMake's `find_package` command provides a syntax for components but does not define any standard semantic meaning for what a component does when requested or provided.  Some packages define one target per component.  Others provide groups of targets per component (e.g. under a "Development" component).  Components have a per-package meaning.

---

## Comment 10

> Username: DenizThatMenace  
> Created_at: 2019-10-28 21:10:07 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547147212  

Thank you, @bradking, for the clarification.  
  
@pdimov   
I am not sure what an alternative name for `all` should be. I liked `all`, because it is simple, short and I did not expect any `Boost.All` library.  
  
Using it in all uppercase (`ALL`) would probably distinguish it from a possible `Boost.All` library but could still lead to some confusion with it.    
Using `__all__` I do not really like because it looks like an internal implementation detail.  
  
We could maybe call it `boost`, because I doubt that there will ever be a `Boost.Boost` library. But this could lead to confusion with the `Boost::boost` alias for `Boost::headers`.  
  
`everything` would have the same problems as `all`. (And so does every other "normal" name.)  
  
Do you have any suggestion or preference?

---

## Comment 11

> Username: HDembinski  
> Created_at: 2019-10-29 08:30:31 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547310508  

If think if no component is specified, then it should find all components. That seems more natural in CMake. An "all" component is somehow backwards. You use the component syntax to select a component but then that "component" is a catch-all.

---

## Comment 12

> Username: DenizThatMenace  
> Created_at: 2019-10-29 08:57:33 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547319982  

> If think if no component is specified, then it should find all components. That seems more natural in CMake.   
  
I agree. However, that completely changes the behavior everyone got used to until this change.  
  
Currently, mentioning no component is equivalent to selecting the `headers` component.  
  
But, if this would not be a show-stopper, I am all for it.

---

## Comment 13

> Username: DenizThatMenace  
> Created_at: 2019-10-29 09:06:42 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547323143  

On a second thought.    
If no component means looking for all components, it is not possible to require e.g. a single component and looking for all other ones as optional components.  
  
Then again the question is: Is that a valid use-case? Does anyone want all components optionally?  
  
The current implementation provided in this pull-request is kind of optional anyhow, when requesting all components. "all" means here "all components for which configurations are installed".

---

## Comment 14

> Username: HDembinski  
> Created_at: 2019-10-29 09:51:40 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547340569  

> I agree. However, that completely changes the behavior everyone got used to until this change.  
  
I see, I wasn't aware of this, but I would go for it anyway. Code should be readable. Not specifying anything means: "give me all of Boost". That is backwards compatible to "give me all the header-only libraries", no?

---

## Comment 15

> Username: DenizThatMenace  
> Created_at: 2019-10-29 10:12:31 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547348498  

I implemented that change, which changes the meaning of calling `find_package(Boost)` without any component to search for all components instead of only looking for the header-only libraries.    
The `all` component was removed again.  
  
Please tell me if this is feasible. (Before merging I could squash these two commits into one, if desired.)  
  
@bradking Do you think that would introduce bigger problems with the `FindBoost.cmake` script of CMake? Would that need adjustments, too? (Possibly with some new policy?) Or are there some gradual migration steps, which would not break usage of `FindBoost.cmake`?  
  
Best would probably be, if `FindBoost.cmake` does not delegate to `BoostConfig.cmake` if `CONFIG` option is missing.

---

## Comment 16

> Username: DenizThatMenace  
> Created_at: 2019-10-29 10:29:11 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547354982  

> Not specifying anything means: "give me all of Boost". That is backwards compatible to "give me all the header-only libraries", no?  
  
That is correct.    
Old CMake-code will do more but should still work... in general.  
  
The exception to this rule is, that if only configuration files for header-only Boost libraries are installed on the system and the `REQUIRED` flag is given to `find_package(Boost)` my implementation will bail out with an error complaining that it cannot find any Boost component (ignoring the `headers` component).  
  
But maybe that is such a minor problem that we could ignore it!?

---

## Comment 17

> Username: dennisklein  
> Created_at: 2019-10-29 11:17:09 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547372211  

> But maybe that is such a minor problem that we could ignore it!?  
  
Please do not ignore it.  
  
Furthermore, the list of components searched should be generated (the boost build system has all the information) and not globbed in the filesystem which can pick up false positives. The code still needs to be able to deal with some of the components not being installed even though compiled (that can be the case on any distro that packages each library separately).

---

## Comment 18

> Username: pdimov  
> Created_at: 2019-10-29 11:58:23 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547385867  

No, the boost build system does not have all the information. First, you can do `b2 install --with-this` and then later `b2 install --with-that`. Second, you can `apt install libboost-this`, which doesn't involve the build system at all.

---

## Comment 19

> Username: pdimov  
> Created_at: 2019-10-29 11:59:18 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547386134  

I think that changing the current behavior of `find_package(Boost)` is a complete non-starter.

---

## Comment 20

> Username: DenizThatMenace  
> Created_at: 2019-10-29 12:30:25 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547396570  

> The exception to this rule is, that if only configuration files for header-only Boost libraries are installed on the system and the `REQUIRED` flag is given to `find_package(Boost)` my implementation will bail out with an error complaining that it cannot find any Boost component (ignoring the `headers` component).  
  
Travis hit exactly that error: https://travis-ci.org/boostorg/boost_install/jobs/604350273  
  
  
> I think that changing the current behavior of `find_package(Boost)` is a complete non-starter.  
  
I am not insisting on changing the current behavior of `find_package(Boost)`.    
But then again my original question stands, what should be the alternative name for the `all` component?

---

## Comment 21

> Username: HDembinski  
> Created_at: 2019-10-29 15:13:08 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547471461  

> Please do not ignore it.  
  
> I think that changing the current behavior of find_package(Boost) is a complete non-starter.  
  
If you disagree, then I would like to hear your arguments. I gave arguments why "COMPONENTS all" is awkward. If it is not safe to change the default behavior of find_package(Boost) then fine. But I think the default behavior right now is not intuitive at all for someone used to CMake.

---

## Comment 22

> Username: pdimov  
> Created_at: 2019-10-29 15:53:53 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547493081  

At the moment, we're trying to get people to distribute our CMake configuration files so that CMake+Boost users can (gradually) migrate to them rather than relying on FindBoost.  
  
Even adding functionality in a compatible way is a bit premature, let alone changing things in a deliberately incompatible way.

---

## Comment 23

> Username: DenizThatMenace  
> Created_at: 2019-10-30 10:29:42 UTC  
> Updated_at: 2019-10-31 12:29:39 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-547835060  

@pdimov What do you think of the following alternative name suggestions for the `all` component?  
  
* `everything`  
* `complete`  
* `COMPLETE`  
* `ALL_COMPONENTS`  
* `all_libraries`  
* `all_boost_libs`  
  
As `find_package` can be called omitting the `COMPONENTS` keyword if the `REQUIRED` keyword is given, one could call it with the following syntax:  
  
```cmake  
find_package( Boost REQUIRED <all-component-name> )  
```  
  
This looks quite nice for `ALL_COMPONENTS` or `COMPLETE` or `everything`:  
```cmake  
find_package( Boost REQUIRED ALL_COMPONENTS )  
```  
```cmake  
find_package( Boost REQUIRED COMPLETE )  
```  
```cmake  
find_package( Boost REQUIRED everything )  
```  
  
For not requiring Boost and optionally looking for all components, it would then look like this:  
```cmake  
find_package( Boost OPTIONAL_COMPONENTS ALL_COMPONENTS )  
```  
```cmake  
find_package( Boost OPTIONAL_COMPONENTS COMPLETE )  
```  
```cmake  
find_package( Boost OPTIONAL_COMPONENTS everything )  
```  
  
I like the `ALL_COMPONENTS` name. And I doubt that there will ever be a Boost library called *Boost.ALL_COMPONENTS* (or *Boost.All_Components* or *Boost.AllComponents*).  
  
What do you think?  
  
edit: I added the `everything` name to the list. I like that one, too. It feels more natural.    
However, I am not sure if the risk of a possible *Boost.Everything* is problematic... Then again, the usage of `everything` for the described use-case would have come first so a later created library shouldn't use that name and could instead call itself *Boost.All*. ;-)

---

## Comment 24

> Username: pdimov  
> Created_at: 2019-10-31 13:34:40 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-548377491  

At the moment I tend towards "ALL" as the component name. "ALL_COMPONENTS" looks like a keyword.  
  
I also think that "ALL", if requested, should not be combined with other component names. That is, `find_package(Boost COMPONENTS ALL filesystem)` should not be allowed. The supported syntaxes should be `find_package(Boost COMPONENTS ALL)` and `find_package(Boost COMPONENTS filesystem regex)`.

---

## Comment 25

> Username: DenizThatMenace  
> Created_at: 2019-10-31 14:10:44 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-548393758  

How about combining it optionally?  
  
```cmake  
find_package(Boost COMPONENTS filesystem OPTIONAL_COMPONENTS ALL)  
```  
Should that be allowed?

---

## Comment 26

> Username: pdimov  
> Created_at: 2019-10-31 14:33:10 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-548404303  

This (assuming REQUIRED is added?) does have a sensible meaning (give me ALL, but only `filesystem` is required), but I think we can safely disallow it, at least at first. I see ALL as a separate mode.  
  
In the envisaged use case, the top project doesn't know which components are required in the subprojects in order to REQUIRE them, so subprojects will in any case need to check Boost_filesystem_FOUND or `TARGET Boost::filesystem` themselves and error out, if they require `filesystem`.  
  
In any event, BoostConfig is supposed to support repeated `find_package` calls, so the above can be spelled as  
  
```  
find_package(Boost REQUIRED filesystem)  
find_package(Boost OPTIONAL_COMPONENTS ALL)  
```

---

## Comment 27

> Username: DenizThatMenace  
> Created_at: 2019-10-31 15:21:42 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-548427636  

I agree with that.  
  
I will change my pull-request accordingly and squash all commits together into a single commit.  
  
---  
  
On your question regarding `REQUIRED` and `COMPONENTS`:  
  
`REQUIRED` alone (or only together with `OPTIONAL_COMPONENTS`) only requires `BoostConfig.cmake` to be found (and thereby `Boost::headers` to be found/created).    
Whereas if any (non-optional) component is requested, `REQUIRED` and `COMPONENTS` are equivalent and require that component to be found, too. (Of course, if `BoostConfig.cmake` or `Boost::headers` could not be found it still fails).  
  
Using neither `REQUIRED` nor `COMPONENTS` (but possibly `OPTIONAL_COMPONENTS`) will always succeed, even if Boost or any of the optionally requested components could not be found.

---

## Comment 28

> Username: DenizThatMenace  
> Created_at: 2019-10-31 19:25:33 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-548531842  

I changed my pull-request as discussed and tested it.    
It works as expected. :grin:  
  
if the new meta-component `ALL` is not the only component given to the call to `find_package` `BoostConfig.cmake` will now issue an error (with a message indicating how to fix it) but will continue searching for all components (ignoring the additional components given).    
However, CMake will automatically stop after its *configuration* step and not proceed into its *generate* step (as is normal when non-fatal errors occur).  
  
This new component always works, whether `find_package` was triggered in "config" or "module" mode (or in the "try-module-first-then-config" mode). If triggered through `FindBoost.cmake` (in "module" or "try-module-first-then-config" mode) `FindBoost.cmake` might issue a warning if `Boost_DEBUG` or `Boost_VERBOSE` were set.     
(I will provide a pull-request to `FindBoost.cmake` fixing that warning, after the current pull-request was merged.)  
  
Oh, and I changed the Boost version-number in the examples to Boost 1.72 assuming this pull-request will be merged into Boost 1.72. :wink:

---

## Comment 29

> Username: pdimov  
> Created_at: 2019-10-31 19:34:55 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-548535134  

> FindBoost.cmake might issue a warning if Boost_DEBUG or Boost_VERBOSE were set.  
  
Why is that?

---

## Comment 30

> Username: DenizThatMenace  
> Created_at: 2019-10-31 19:46:14 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-548539445  

Because `FindBoost.cmake` tries to be clever and to set some legacy-variables in its `_boost_set_legacy_variables_from_config` function. However, it then realizes that no target `Boost::ALL` was created and issues the following warning if `Boost_DEBUG` or `Boost_VERBOSE` is set:  
  
```  
Could not find imported target for required component 'ALL'. Legacy variables for this component might be missing. Refer to the documentation of your Boost installation for help on variables to use.  
```  
  
For similar reasons I had to set the variable `Boost_ALL_FOUND`. However, if that would not be available, `FindPackageHandleStandardArgs.cmake` would even fail with an error.  
  
The fix for `FindBoost.cmake` that I would provide prevents both problems. But for backward-compatibility with older `FindBoost.cmake`/CMake versions I would recommend to still set `Boost_ALL_FOUND` (as I did in this pull-request).

---

## Comment 31

> Username: dennisklein  
> Created_at: 2019-10-31 21:25:28 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-548575320  

> For similar reasons I had to set the variable Boost_ALL_FOUND. However, if that would not be available, FindPackageHandleStandardArgs.cmake would even fail with an error.  
  
What is the problem with setting `Boost_ALL_FOUND`?

---

## Comment 32

> Username: DenizThatMenace  
> Created_at: 2019-10-31 21:35:32 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-548578652  

> > For similar reasons I had to set the variable Boost_ALL_FOUND. However, if that would not be available, FindPackageHandleStandardArgs.cmake would even fail with an error.  
>   
> What is the problem with setting `Boost_ALL_FOUND`?  
  
There is no problem with setting `Boost_ALL_FOUND`.    
There is a problem with **not** setting `Boost_ALL_FOUND`.  
  
If `BoostConfig.cmake` is triggered through `FindBoost.cmake` and `Boost_ALL_FOUND` is not set, the following error will be triggered from `FindPackageHandleStandardArgs.cmake`:  
  
```  
CMake Error at .../Modules/FindPackageHandleStandardArgs.cmake:146 (message):  
  Could NOT find Boost: missing: ALL (found  
  /usr/lib/cmake/Boost-1.71.0/BoostConfig.cmake  
  (found suitable version "1.71.0", minimum required is "1.71.0"))  
Call Stack (most recent call first):  
  .../Modules/FindPackageHandleStandardArgs.cmake:159 (_FPHSA_FAILURE_MESSAGE)  
  .../Modules/FindPackageHandleStandardArgs.cmake:388 (_FPHSA_HANDLE_FAILURE_CONFIG_MODE)  
  .../FindBoost.cmake:465 (find_package_handle_standard_args)  
  CMakeLists.txt:21 (find_package)  
```

---

## Comment 33

> Username: dennisklein  
> Created_at: 2019-10-31 21:39:16 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-548579846  

> I had to set the variable Boost_ALL_FOUND  
  
Sounded like you rather did not want to set it.  
  
> The fix for FindBoost.cmake that I would provide prevents both problems.  
  
Sounded like you wanted to patch `FindBoost.cmake` so that it does not require `Boost_ALL_FOUND`. Or what did you mean by **both** problems?

---

## Comment 34

> Username: dennisklein  
> Created_at: 2019-10-31 21:43:35 UTC  
> Updated_at: 2019-10-31 21:57:07 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-548581222  

So there is really just one problem with `FindBoost` to be adressed: Teach it about the new `ALL` component category, which has no imported target.  
  
My point: Setting `Boost_ALL_FOUND` is not just for backwards compatibility or something *annoying* required by the *cleverness* of `FindBoost` as you put it. It is required to comply with acting CMake conventions.

---

## Comment 35

> Username: DenizThatMenace  
> Created_at: 2019-10-31 21:58:36 UTC  
> Updated_at: 2019-10-31 22:02:47 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-548585826  

> > I had to set the variable Boost_ALL_FOUND  
>   
> Sounded like you rather did not want to set it.  
  
Partially.    
Currently, `Boost_ALL_FOUND` has no real meaning and is only there to please `FindBoost.cmake` and `FindPackageHandleStandardArgs.cmake`.    
It always has the same value as `Boost_FOUND`.  
  
If `Boost_FOUND` is not set that means that not even the header-only Boost libraries were found (which always need to be found) and therefore no `Boost::*` target (not even `Boost::headers`) is created.    
If it is found, however, it does not matter how many other Boost libraries were installed. If a configuration for them is available, they will automatically be found. (I do not know how that could fail and the `Boost_<component>_FOUND` variable therefore not be set?)  
  
> > The fix for FindBoost.cmake that I would provide prevents both problems.  
>   
> Sounded like you wanted to patch `FindBoost.cmake` so that it does not require `Boost_ALL_FOUND`. Or what did you mean by **both** problems?  
  
Yes, that would fix that, too. But the other problem is the warning message I mentioned, which only can be prevented if patching `FindBoost.cmake`.    
As a side-effect, `Boost_ALL_FOUND` would no longer be needed to prevent `FindPackageHandleStandardArgs.cmake` from failing with the above shown error.  
  
> So there is really just one problem with `FindBoost` to be adressed: Teach it about the new ALL component category.  
  
I will. (That is what my mentioned patch is doing.)    
But that patch alone would not be backward-compatible with older CMake/`FindBoost.cmake` versions.    
That would mean the `ALL` component could only be used with very new CMake/`FindBoost.cmake` versions, if one does not explicitly use the "config" mode when calling `find_package(Boost REQUIRED ALL)`.  
  
With the solution I provided (that is setting `Boost_ALL_FOUND`, too) this new feature would also work with current CMake/`FindBoost.cmake` versions (but sadly issue a warning if `Boost_DEBUG` or `Boost_VERBOSE` is set).

---

## Comment 36

> Username: DenizThatMenace  
> Created_at: 2019-10-31 22:11:32 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-548589469  

To make it more clear, [this](https://gitlab.kitware.com/dbahadir/cmake/commit/d5fca8cbdad3e68977f67c0e9ffaf3fe673b7756) is the (simple) patch I will provide to `FindBoost.cmake` to fix that mentioned warning (and as side-effect make setting `Boost_ALL_FOUND` superfluous):  
```diff  
diff --git a/Modules/FindBoost.cmake b/Modules/FindBoost.cmake  
index 744d2c7895cf75d0f55c3602fd082bbcc9297886..71a5ff6cb66a38cf666181f1cac06d0671ac6e1e 100644  
--- a/Modules/FindBoost.cmake  
+++ b/Modules/FindBoost.cmake  
@@ -449,6 +449,9 @@ if (NOT Boost_NO_BOOST_CMAKE)  
     # Convert component found variables to standard variables if required  
     # Necessary for legacy boost-cmake and 1.70 builtin BoostConfig  
     if(Boost_FIND_COMPONENTS)  
+      # Ignore the meta-component "ALL", introduced by Boost 1.72  
+      list(REMOVE_ITEM Boost_FIND_COMPONENTS "ALL")  
+  
       foreach(_comp IN LISTS Boost_FIND_COMPONENTS)  
         if(DEFINED Boost_${_comp}_FOUND)  
           continue()  
```  
  
Of course, all subject to the condition that the current pull-request will make it into Boost 1.72.0. (Otherwise, I would need to adjust the comment in the diff.)

---

## Comment 37

> Username: dennisklein  
> Created_at: 2019-10-31 22:25:08 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-548593386  

Again, I disagree with your argument. I think setting `Boost_ALL_FOUND` is just fine. Have you tried `find_package(Boost COMPONENTS ALL CONFIG)`, your proposed patch in `FindBoost` would not be effective?

---

## Comment 38

> Username: DenizThatMenace  
> Created_at: 2019-10-31 22:47:33 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-548599011  

> Again, I disagree with your argument. I think setting `Boost_ALL_FOUND` is just fine.  
  
What argument? I did not say that setting `Boost_ALL_FOUND` is not fine. I said it is redundant and currently needed for preventing an error with `FindPackageHandleStandardArgs.cmake`.  
  
But I am fine with it being there. I personally just do not see the need for it (were it not to prevent said error).  
  
> Have you tried `find_package(Boost COMPONENTS ALL CONFIG)`, your proposed patch in `FindBoost` would not be effective?  
  
Of course. I tried all possible combinations.    
"module" mode, "config" mode, "try-module-first-then-config" mode and all with diverse combinations of `REQUIRED`, `COMPONENTS`, `OPTIONAL_COMPONENTS`, `ALL` and extra components.  
  
My pull-request works fine with all combinations (with or without a patched `FindBoost.cmake`).    
Only situation were it fails (expectedly) is with `ALL` and another component together in the call to `find_package`.  
  
In particular, if you are using "config" mode, `FindBoost.cmake` is not called and `Boost_ALL_FOUND` would not be needed at all.

---

## Comment 39

> Username: pdimov  
> Created_at: 2019-10-31 23:05:06 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-548603142  

The argument is that since `ALL` is not supported by FindBoost, there is never a need to use `find_package(Boost COMPONENTS ALL)` without explicitly requesting BoostConfig f.ex. via `CONFIG`.

---

## Comment 40

> Username: DenizThatMenace  
> Created_at: 2019-11-01 00:02:03 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-548615672  

> The argument is that since `ALL` is not supported by FindBoost, there is never a need to use `find_package(Boost COMPONENTS ALL)` without explicitly requesting BoostConfig f.ex. via `CONFIG`.  
  
No, sorry, that was not my argument.  
  
But as there will never be a target `Boost::ALL` and as it depends on the system¹ what `Boost::*` targets exactly will be created as result of using `ALL`, `Boost_ALL_FOUND` does not really tell you much. It only tells you that all on this system installed Boost configurations belonging to the currently processed `BoostConfig.cmake` were found. But what targets it found, it does not tell you.    
(That is what the new variable `Boost_ALL_TARGETS` is for.)  
  
¹ as you correctly mentioned in https://github.com/boostorg/boost_install/pull/19#issuecomment-547385867, the result of `ALL` depends on what Boost libraries are installed on the system and cannot be hard-coded in `BoostConfig.cmake`.  
  
Actually, I do not see the need to force everyone to use `find_package(Boost CONFIG COMPONENTS ALL)` (using the config mode explicitly), when omitting the `CONFIG` (and not using `MODULE` explicitly) can achieve the same.  
  
---  
  
What would be more useful would be to provide some deprecation mechanism for CMake targets (as described [here](https://gitlab.kitware.com/cmake/cmake/issues/17824#note_486871)) which would allow us to deprecate the use of `Boost::boost`, `Boost::diagnostic_definitions`, `Boost::disable_autolinking` and `Boost::dynamic_linking`.  
  
Likewise, one could also try to deprecate the use of `find_package(Boost)` without any component for creating `Boost::headers` and, in the far future, could interpret that call without any component as an alternative to requesting the `ALL` component.  
  
But all of that has nothing to do with this pull-request or the associated issue.

---

## Comment 41

> Username: pdimov  
> Created_at: 2019-11-01 00:09:28 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-548617150  

> No, sorry, that was not my argument.  
  
Yes, sorry. My point was that this was Dennis's argument. Not yours.

---

## Comment 42

> Username: DenizThatMenace  
> Created_at: 2019-11-01 00:29:55 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-548621046  

> > No, sorry, that was not my argument.  
>   
> Yes, sorry. My point was that this was Dennis's argument. Not yours.  
  
Thanks for the clarification  :relieved:

---

## Comment 43

> Username: DenizThatMenace  
> Created_at: 2019-11-01 23:48:14 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-548986013  

@pdimov BTW, I consider this pull-request as complete.    
If you do not have any objections I would kindly ask you to accept this pull-request and merge it into the official repository (from where it hopefully makes it into *Boost 1.72*).

---

## Comment 44

> Username: pdimov  
> Created_at: 2019-11-05 14:56:10 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-549858772  

This feature request came in a bit too late for 1.72, which is now closed for major changes. I will merge it to develop after 1.72 ships, and it will in all probability make its way into 1.73.

---

## Comment 45

> Username: DenizThatMenace  
> Created_at: 2019-11-05 16:09:13 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-549890569  

It already is?    
I thought there is always coming an announcement via the mailing-list first (which I did not see).  
  
So, if it is targeted for Boost 1.73, I should then probably update the examples in the documentation to use that version (to prevent confusion).

---

## Comment 46

> Username: DenizThatMenace  
> Created_at: 2019-11-05 16:24:38 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-549897391  

> So, if it is targeted for Boost 1.73, I should then probably update the examples in the documentation to use that version (to prevent confusion).  
  
I just did.

---

## Comment 47

> Username: DenizThatMenace  
> Created_at: 2019-11-05 16:46:20 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-549907006  

For you to note:  
  
I created the aforementioned merge-request for CMake's `FindBoost.cmake` to prevent the warning regarding the `ALL` component. [[1]](https://gitlab.kitware.com/cmake/cmake/merge_requests/3996)  
  
It will hopefully make it into CMake 3.17, which will probably be released around the same time as Boost 1.73.  
  
[1] https://gitlab.kitware.com/cmake/cmake/merge_requests/3996

---

## Comment 48

> Username: DenizThatMenace  
> Created_at: 2019-11-06 15:18:23 UTC  
> Url: https://github.com/boostorg/boost_install/pull/19#issuecomment-550356739  

One more note:  
  
The merge-request for CMake's `FindBoost.cmake` has already been merged and will even go into CMake 3.16 which will be released within the next 2 weeks, I guess.  
  
So, everything is prepared for this pull-request to work without any problems. :grin:

---
