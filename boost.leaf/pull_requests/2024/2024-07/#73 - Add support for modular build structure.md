# #73 Add support for modular build structure. [Merged]

> Username: grafikrobot  
> Created at: 2024-07-20 22:34:38 UTC  
> Updated at: 2025-05-02 16:13:41 UTC  
> Merged at: 2025-05-02 16:13:41 UTC  
> Closed at: 2025-05-02 16:13:41 UTC  
> Url: https://github.com/boostorg/leaf/pull/73  

This is part of the effort to make the Boost libraries "modular" for build and consumption. See https://lists.boost.org/Archives/boost/2024/01/255704.php and https://github.com/grafikrobot/boost-b2-modular/blob/b2-modular/README.adoc for more information.  
  
This PR depends on the following other PRs being merged to both develop and master branches of the respective repos:  
  
- https://github.com/boostorg/boost/pull/854  
  
This PR will be changed to ready for review, i.e. not draft, when the above are merged. Do not merge this one until that time.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2024-08-18 16:06:40 UTC  
> Url: https://github.com/boostorg/leaf/pull/73#issuecomment-2295312163  

Please review and merge this PR at your earliest convenience.

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2024-08-25 21:54:10 UTC  
> Url: https://github.com/boostorg/leaf/pull/73#issuecomment-2309008161  

@zajo replying here as it's easier for me to keep track of questions/issues..  
  
>I use b2 only to build the unit tests, as long as this functionality doesn't break, I don't think LEAF needs this. What user would benefit from this change and how?  
  
Some benefits:  
  
- It makes it possible for user to directly consume the library from B2 in a modular way, which int he case of LEAF would be a standalone mode since it doesn't have public Boost dependencies.  
- It makes it easier to package the library in a modular way. I.e. vcpkg and Conan wont have to do heroics to modularize the Boost release. As modular use will be directly supported.  
  
I understand that other benefits from other use cases might not apply to LEAF because it doesn't depend on other Boost libraries (neither does mine, Predef, and that doesn't even depend on Boost for tests). But if at some future point another Boost library, or perhaps a non-Boost library, depends on LEAF and uses B2 it will need the public declaration of the project and project target to make it work (to add the include dir path and any build requirements).  
  
This generally brings B2 on par with what's in the CML. Hence if you think having a CML is useful.. Then having this PR is useful for the same reasons.

---
