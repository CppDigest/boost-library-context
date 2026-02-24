# #531 Add "cxxstd" json field to meta/libraries.json [Merged]

> Username: eldiener  
> Created at: 2020-12-16 07:05:41 UTC  
> Updated at: 2020-12-17 09:38:49 UTC  
> Merged at: 2020-12-16 07:56:50 UTC  
> Closed at: 2020-12-16 07:56:50 UTC  
> Url: https://github.com/boostorg/gil/pull/531  

### Description  
  
<!-- What does this pull request do? -->  
  
The "cxxstd" json field is being added to each Boost library's meta json information for libraries whose minumum C++ standard compilation level is C++11 on up. The value of this field matches one of the values for 'cxxstd' in Boost.Build. The purpose of doing this is to provide information for the Boost website documentation for each library which will specify the minimum C++ standard compilation that an end-user must employ in order to use the particular library. This will aid end-users who want to know if they can successfully use a Boost library based on their C++ compiler's  compilation level, without having to search the library's documentation to find this out.  
  
<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
- https://lists.boost.org/Archives/boost/2020/12/250543.php  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2020-12-16 07:57:42 UTC  
> Url: https://github.com/boostorg/gil/pull/531#issuecomment-745866062  

@ eldiener  Thank you!

---
