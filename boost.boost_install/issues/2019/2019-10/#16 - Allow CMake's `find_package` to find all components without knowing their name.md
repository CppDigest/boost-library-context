# #16 - Allow CMake's `find_package` to find all components without knowing their name. [Closed]

> Username: DenizThatMenace  
> Created at: 2019-10-25 13:51:01 UTC  
> Updated at: 2020-01-07 19:12:24 UTC  
> Closed at: 2020-01-07 19:12:24 UTC  
> Url: https://github.com/boostorg/boost_install/issues/16  

Problem:  
-----------  
Currently, if one uses CMake's `find_package` command to look for a `BoostConfig.cmake` file, one has to list all (desired) components (aka Boost libraries with binaries) explicitly, like this:  
  
```cmake  
find_package( Boost 1.71.0 REQUIRED  
              COMPONENTS unit_test_framework filesystem program_options ...  # all compiled boost libs  
              CONFIG)  
```  
  
This is tedious, particularly if one wants to find all Boost libraries, to make all targets available to one's CMake project (and sub-projects thereof).    
With every new Boost version one needs to check and possibly update that listing to incorporate new libraries.  
  
Suggested Feature:  
-------------------------  
It would be very helpful if a single component, let's call it `all`, would exist which makes `find_package` automatically search for all Boost libraries (may they be binary or header-only) belonging to the desired version.  
  
As a result, imported targets for all Boost components as well as the header-only targets (at least `Boost::headers` and `Boost::boost`) and maybe some other useful imported targets should be created.  
  
Additionally, a special variable, let's call it `Boost_ALL_TARGETS`, should be set which contains the names of all these imported targets.    
This would allow to comfortably promote all these targets to global scope, using the following command:  
```cmake  
set_target_properties( ${Boost_ALL_TARGETS}  
                       PROPERTIES IMPORTED_GLOBAL TRUE )  
```  
  
At the end, one could do the following in any (sub-)project of one's CMake project and make all of Boost available to all other projects without needing to know at this point what Boost libraries are contained in the requested version.  
  
```cmake  
  
find_package( Boost ${ANY_BOOST_VERSION} REQUIRED  
              COMPONENTS all  
              CONFIG  
)  
if ( Boost_FOUND )  
    set_target_properties( ${Boost_ALL_TARGETS}  
                           PROPERTIES IMPORTED_GLOBAL TRUE )  
endif ()  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2019-10-25 14:15:55 UTC  
> Url: https://github.com/boostorg/boost_install/issues/16#issuecomment-546372214  

What is the use case of this functionality? A project knows which Boost libraries it needs, so it can always specify them; and if a new Boost library appears, it's not possible for an existing project to somehow evolve by itself to need it.

---

## Comment 2

> Username: dennisklein  
> Created at: 2019-10-25 14:18:16 UTC  
> Updated at: 2019-10-25 14:23:59 UTC  
> Url: https://github.com/boostorg/boost_install/issues/16#issuecomment-546373130  

> Additionally, a special variable, let's call it Boost_ALL_TARGETS, should be set which contains the names of all these imported targets.  
  
Especially +1 for this. It could be even worth having this kind of introspection provided generically by CMake that works for any `find_package` call.  
  
edit: Use case for this is to be able to patch imported targets with *somewhat generic* code in project-local find module wrappers, one example is given by OP. Another would be to set missing properties.

---

## Comment 3

> Username: DenizThatMenace  
> Created at: 2019-10-25 14:24:58 UTC  
> Updated at: 2019-10-25 14:28:45 UTC  
> Url: https://github.com/boostorg/boost_install/issues/16#issuecomment-546375594  

The use-case is that I do no longer have to care about importing specific Boost libraries. All available ones are always available as imported targets, that I can use in my project.  
  
If for example during development I realize I need to use another Boost library, I can just use it and do a `target_link_libraries( MyTarget PRIVATE Boost::some_lib )` and do not have to further think about finding that library, too.  
  
This is helpful in particular for larger (monolithic) projects, with different sub-parts, that all want to use some boost libraries, but require to always need the same version and do not want to search for Boost libraries in every subdirectory, again.  
  
CMake needs quite some time for searching the same Boost libraries over and over again. (I am talking from experience.) And there comes in handy the promotion to global scope (or just searching for Boost in the top-level `CMakeLists.txt` file).  
  
edit: Possibly, using the `CONFIG` mode of `find_package` might be faster but still, why should I look for the same libraries over and over again?

---

## Comment 4

> Username: pdimov  
> Created at: 2019-10-25 18:20:09 UTC  
> Url: https://github.com/boostorg/boost_install/issues/16#issuecomment-546459422  

> Possibly, using the `CONFIG` mode of `find_package` might be faster...  
  
I would certainly hope it is. :-)

---

## Comment 5

> Username: HDembinski  
> Created at: 2019-10-29 08:25:12 UTC  
> Url: https://github.com/boostorg/boost_install/issues/16#issuecomment-547308687  

I think this is a good idea for reasons pointed out. It also allows people to kickstart a new project with minimum amount of effort (just import all of Boost). Then, as needed, folks can reduce compile and link times by only selecting the libraries that they really need. This is something you typically do after the prototype settled down a bit.

---

## Comment 6

> Username: HDembinski  
> Created at: 2019-10-29 08:28:16 UTC  
> Url: https://github.com/boostorg/boost_install/issues/16#issuecomment-547309686  

I think the following command finds all of Boost.  
```  
find_package( Boost_1.71.0 )  
```  
An "all" component makes no sense, this seems more natural and is even less to type.  
  
Similarly, I think the command should set `Boost_TARGETS` instead of `Boost_ALL_TARGETS`

---

## Comment 7

> Username: DenizThatMenace  
> Created at: 2019-10-29 10:45:15 UTC  
> Url: https://github.com/boostorg/boost_install/issues/16#issuecomment-547360879  

If the variable is called `Boost_TARGETS` then that should probably always be set, even if not all components are requested.  
  
Should it then contain the backwards-compatibility targets (`Boost::boost`, `Boost::diagnostic_definitions`, `Boost::disable_autolinking` and `Boost::dynamic_linking`), too?

---

## Comment 8

> Username: HDembinski  
> Created at: 2019-10-29 10:57:33 UTC  
> Url: https://github.com/boostorg/boost_install/issues/16#issuecomment-547365492  

> If the variable is called Boost_TARGETS then that should probably always be set, even if not all components are requested.  
  
Good point.  
  
The other question is for experts to answer, I would say no, but I am not an expert. Perhaps, to avoid confusion, the variable could be called `Boost_LIBRARY_TARGETS`?

---

## Comment 9

> Username: DenizThatMenace  
> Created at: 2019-10-31 11:53:33 UTC  
> Url: https://github.com/boostorg/boost_install/issues/16#issuecomment-548335396  

I would like to know if the following use-case should be handled as an error or should just succeed:  
  
---  
  
Only header-only *Boost* libraries are installed on the system (along with the `BoostConfig.cmake` script). So, only the `Boost::headers` target could be created.  
  
Should the following call bail out with an error saying that it could not find any *Boost* component or should it just succeed and only create the `Boost::headers` target?  
  
```cmake  
find_package(Boost REQUIRED COMPONENTS all)  
```  
  
Note: Let alone the discussion regarding an alternative name for the `all` component.

---

## Comment 10

> Username: pdimov  
> Created at: 2019-10-31 13:30:48 UTC  
> Url: https://github.com/boostorg/boost_install/issues/16#issuecomment-548375669  

Yes, this should work.

---

## Comment 11

> Username: mathstuf  
> Created at: 2019-11-05 14:50:48 UTC  
> Url: https://github.com/boostorg/boost_install/issues/16#issuecomment-549856428  

FWIW, I think I've normally seen a component-less `find_package` brings in all known components. Though I think `FindBoost.cmake` has not behaved that way historically. I'm not sure which is better here.

---

## Comment 12

> Username: DenizThatMenace  
> Created at: 2019-11-05 16:22:04 UTC  
> Url: https://github.com/boostorg/boost_install/issues/16#issuecomment-549896221  

I recommend, to not change the current behavior of component-less `find_package(Boost)` for now, because that would be quite different to the established behavior of `FindBoost.cmake`.  
  
Instead I recommend to advocate for always using `find_package(Boost...)` with components and in particular with the `headers` component if only header-only Boost is requested.  
After some years of advocating that practice we could change the component-less `find_package(Boost)` to mean the same as the new `ALL` component.

---

## Comment 13

> Username: mathstuf  
> Created at: 2019-11-05 22:03:46 UTC  
> Url: https://github.com/boostorg/boost_install/issues/16#issuecomment-550043042  

I think the difference has been in CMake-built projects versus non-CMake-built projects. In a CMake project, unless export sets are done basically on a per-target basis, you're stuck with either exposing targets at an export set granularity. Generally projects have just a single export set. Qt5 is also no-components-by-default because it has to go and find targets to expose while projects like VTK and ParaView always make all their targets but only guarantee that those explicitly requested actually "work" (in that their transitive dependency packages are actually found).
