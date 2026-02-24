# #99 C++11, Fix CI, fix use of deprecated timer, fix group assignment operator. [Merged]

> Username: jeking3  
> Created at: 2024-05-15 13:25:00 UTC  
> Updated at: 2024-05-15 17:21:12 UTC  
> Merged at: 2024-05-15 17:21:05 UTC  
> Closed at: 2024-05-15 17:21:05 UTC  
> Url: https://github.com/boostorg/format/pull/99  

Dependencies like optional and smart_ptr will no longer support C++03 therefore the new minimum language level is C++11, and all the CI jobs for both C++98 and C++03 have been removed.  Removed MSVC 2010 and 2012 from CI, moved some windows jobs to GHA.  
      
Fixed use of the deprecated top level timer class as it was causing build problems using &lt;warnings-as-errors>.  
      
Fixed -Wdeprecated-cast in group.hpp.  
Fixed -Winvalid-source-encoding in sample_new_features.cpp.  
Fixed -Wself-assign-overloaded in format_test2.cpp.  
      
These changes also allow b2 to work properly when executed from within the format directory.  
  
This closes #91.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2024-05-15 14:08:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/format/pull/99#pullrequestreview-2058148137  

📁 Jamfile

```diff
  19 |-       <toolset>gcc:<cxxflags>-ansi
  20 |-       <toolset>gcc:<cxxflags>-pedantic
  12 |+         <warnings>on
```

> Username: pdimov  
> Created_at: 2024-05-15 14:08:17 UTC  
> Url: https://github.com/boostorg/format/pull/99#discussion_r1601723657  

The previous warning level was essentially `<warnings>pedantic`; `<warnings>on` is much less strict.

> Username: jeking3  
> Created_at: 2024-05-15 14:48:30 UTC  
> Updated_at: 2024-05-15 14:48:31 UTC  
> Url: https://github.com/boostorg/format/pull/99#discussion_r1601791665  

Thanks, I put pedantic back, and we'll see what complains in CI.


---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2024-05-15 14:10:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/format/pull/99#pullrequestreview-2058153391  

📁 include/boost/format/group.hpp

```diff
  39 |-     group0()      {}
  39 |+     BOOST_FORCEINLINE group0() {}
  40 |+ #if !defined(BOOST_NO_CXX11_DELETED_FUNCTIONS)
```

> Username: pdimov  
> Created_at: 2024-05-15 14:10:15 UTC  
> Url: https://github.com/boostorg/format/pull/99#discussion_r1601726810  

What compiler of the supported ones doesn't have deleted functions?

> Username: jeking3  
> Created_at: 2024-05-15 17:07:07 UTC  
> Url: https://github.com/boostorg/format/pull/99#discussion_r1601985218  

This was prior to setting the minimum language level to C++11 due to dependencies doing the same.


---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2024-05-15 14:11:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/format/pull/99#pullrequestreview-2058155530  

📁 test/format_test2.cpp

```diff
 209 |-     fmt = fmt;
 209 |+     format fmt2 = fmt;
 210 |+     format fmt3(fmt);
```

> Username: pdimov  
> Created_at: 2024-05-15 14:11:03 UTC  
> Url: https://github.com/boostorg/format/pull/99#discussion_r1601728193  

None of these test the assignment operator.


---

## Review 4 [Commented]

> Username: pdimov  
> Created_at: 2024-05-15 14:12:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/format/pull/99#pullrequestreview-2058158942  

Why were the group changes needed?

---

## Comment 5

> Username: jeking3  
> Created_at: 2024-05-15 14:47:58 UTC  
> Url: https://github.com/boostorg/format/pull/99#issuecomment-2112751250  

> Why were the group changes needed?  
  
Example:  
```  
../../boost/format/group.hpp:59:12: error: definition of implicit copy constructor for 'group1<Rational &>' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
   group1& operator=(const group1&);  
```  
  
I thought it would be easier to delete the assignment operator once rather than in each class, but I suppose the complexity goes way up when deriving.

---
