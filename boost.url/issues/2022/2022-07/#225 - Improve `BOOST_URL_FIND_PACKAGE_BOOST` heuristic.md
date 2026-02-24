# #225 - Improve `BOOST_URL_FIND_PACKAGE_BOOST` heuristic [Closed]

> Username: alandefreitas  
> Created at: 2022-07-07 20:57:25 UTC  
> Updated at: 2022-08-03 00:25:26 UTC  
> Closed at: 2022-08-03 00:25:26 UTC  
> Url: https://github.com/boostorg/url/issues/225  

The current heuristic (Boost.Url is root and `"./../../CMakeLists.txt"` exists) for inferring whether `BOOST_URL_FIND_PACKAGE_BOOST` should be implicitly defined is wrong and confusing:  
  
- `"../../../CMakeLists.txt"` existing doesn't mean `./../../` is boost root. Users adding Boost.Url with `add_subdirectory` have the option arbitrarily defined and undefined depending on how deep the Boost.Url subdirectory is.  
- Boost.Url not being root doesn't mean the user shouldn't use `find_package`. In fact, most users running the script as root might expect it to find the boost installation, which is a much simpler path than patching and reinstalling the whole boost superproject.   
  
TL;DR: The heuristic has no false positives (never uses `find_package` when Boost.Url is a subproject of Boost) but it has false negatives (might fail to use `find_package` when Boost.Url is not a subproject of Boost). We rely on the first in our tests, but users will rely on the latter.  
  
To fix it, we need `find_package` and link `boost::headers` when:  
- Boost.Url is root but not a subdirectory of the super project  
- Boost.Url is not root but not a subdirectory of the super project  
  
We need to disable `find_package` and link individual boost targets when:  
- Boost.Url is root and it is a subdirectory of the super project  
- Boost.Url is not root but it is a subdirectory of the super project  
  
So we can infer it with:  
  
- Inferring if Boost.Url is root is trivial. It's already done.  
- Inferring the superproject is less trivial:  
    - If Boost.Url is not root, we can check the main project name (`${PROJECT_NAME}` before calling `project`).  
    - If Boost.Url is root, then we have a few options to infer whether we need `find_package` with  
        - Any set of features more distinctive than a CMakeLists.txt file two directories above  
        - `find_package` and fallback to `"./../../CMakeLists.txt"` only when it fails  
        - Making `BOOST_URL_FIND_PACKAGE_BOOST` an option  
        - ... (?)  
        - All of the above  
  
This way, things could just work: users won't need to patch the boost installation or study the build script to integrate the library, and problems such as #220 would be avoided.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-07-08 06:03:35 UTC  
> Updated at: 2022-07-08 06:03:56 UTC  
> Url: https://github.com/boostorg/url/issues/225#issuecomment-1178589062  

seems like an awful lot of try-hard for such a simple thing, why does it have to be so complicated? http.proto and http.io will need this as well. I'm assuming that if Boost.URL becomes accepted then we will no longer need this after it appears in a release?

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-07-08 06:26:07 UTC  
> Url: https://github.com/boostorg/url/issues/225#issuecomment-1178604048  

The actual thing would probably be less than 10 lines of code. But I'm listing options.  
  
The "TL;DR" would be something like "not being a subdirectory of boost should be the condition for using `find_package`".  
  
All the rest of the issue is about how the current conditional is implemented. why it is problematic, the problems it causes, the options to implement it, related issues, etc...

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-07-08 06:27:58 UTC  
> Url: https://github.com/boostorg/url/issues/225#issuecomment-1178605255  

> Boost.URL becomes accepted then we will no longer need this after it appears in a release  
  
Yes. Once in Boost, I think some people even prefer that no other alternative integration option is provided.
