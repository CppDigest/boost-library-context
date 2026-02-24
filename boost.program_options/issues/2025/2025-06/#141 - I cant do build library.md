# #141 - I cant do build library [Closed]

> Username: k0swel  
> Created at: 2025-06-20 01:51:18 UTC  
> Updated at: 2026-01-22 11:38:49 UTC  
> Closed at: 2026-01-22 11:38:49 UTC  
> Url: https://github.com/boostorg/program_options/issues/141  

I'm trying to compile a library, but I encounter an error from cmake:  
  
```  
CMake Error at libraries/program_options/CMakeLists.txt:28 (target_link_libraries):  
  Target "boost_program_options" links to:  
    Boost::any  
  but the target was not found.  Possible reasons include:  
    * There is a typo in the target name.  
    * A find_package call is missing for an IMPORTED target.  
    * An ALIAS target is missing.  
```  
  
  
I downloaded Boost libraries from here: https://www.boost.org/releases/latest/ (Ubuntu 24.04.1 TLS) and it didnt help me

---

## Comment 1

> Username: vprus  
> Created at: 2025-06-20 08:29:13 UTC  
> Url: https://github.com/boostorg/program_options/issues/141#issuecomment-2990260451  

Thanks for the report.  
  
I would recommend that you ask about this on Slack.
