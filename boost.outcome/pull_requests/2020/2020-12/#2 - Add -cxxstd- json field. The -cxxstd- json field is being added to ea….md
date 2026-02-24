# #2 Add "cxxstd" json field. The "cxxstd" json field is being added to ea… [Closed]

> Username: eldiener  
> Created at: 2020-12-16 06:57:32 UTC  
> Updated at: 2020-12-17 13:37:28 UTC  
> Closed at: 2020-12-16 10:52:38 UTC  
> Url: https://github.com/boostorg/outcome/pull/2  

…ch Boost library's meta json information for libraries whose minumum C++ standard compilation level is C++11 on up. The value of this field matches one of the values for 'cxxstd' in Boost.Build. The purpose of doing this is to provide information for the Boost website documentation for each library which will specify the minimum C++ standard compilation that an end-user must employ in order to use the particular library. This will aid end-users who want to know if they can successfully use a Boost library based on their C++ compiler's  compilation level, without having to search the library's documentation to find this out.

---

## Comment 1

> Username: ned14  
> Created_at: 2020-12-16 10:52:38 UTC  
> Url: https://github.com/boostorg/outcome/pull/2#issuecomment-746091199  

Reimplemented in its proper place. boostorg/outcome won't get updated until Outcome's documentation has been fully ported to Outcome v2.2. This may take some weeks.

---

## Comment 2

> Username: eldiener  
> Created_at: 2020-12-16 14:23:46 UTC  
> Url: https://github.com/boostorg/outcome/pull/2#issuecomment-746369330  

What has your reimplementation have to do with the above PR to the meta information ? The above PR should be valid whenever you do other changes to Outcome unless the minimal level of C++ standard compilation changes with your reimplementation.

---

## Comment 3

> Username: ned14  
> Created_at: 2020-12-16 14:43:44 UTC  
> Url: https://github.com/boostorg/outcome/pull/2#issuecomment-746402469  

You can see your PR at https://github.com/ned14/outcome/commit/18089d769a35dfdaa14dec232bb9659d0879cc99. It implements the same thing.

---

## Comment 4

> Username: eldiener  
> Created_at: 2020-12-17 13:37:28 UTC  
> Url: https://github.com/boostorg/outcome/pull/2#issuecomment-747443498  

OK, thanks !

---
