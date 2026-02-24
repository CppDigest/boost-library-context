# #8 Add ignore_unused_variable_warning() [Closed]

> Username: awulkiew  
> Created at: 2014-05-19 12:42:27 UTC  
> Updated at: 2014-06-02 20:36:28 UTC  
> Closed at: 2014-06-02 20:36:28 UTC  
> Url: https://github.com/boostorg/utility/pull/8  

boost::ignore_unused_variable_warning() function is defined in ConceptCheck but also in the internals of other libraries (GIL, Interval, Test, uBlas, Unordered) probably to not include the whole concept_check.hpp. This pull request adds the tool to the Utility and expands its functionality by the use of variadic template (if supported) or overloads.  
Followed by: http://boost.2283326.n4.nabble.com/Standalone-boost-ignore-unused-variable-warning-td4662481.html

---

## Comment 1

> Username: ericniebler  
> Created_at: 2014-05-19 16:36:20 UTC  
> Url: https://github.com/boostorg/utility/pull/8#issuecomment-43526929  

Thanks. We need this. There's been some talk about doing away with utility and finding "proper" homes for all the utilities. I'm not sure what the status of this is, and I'm also not sure I agree with this, but it's a question I'd like answered before adding another utility.  
  
Also, it'd be nice if you changed the test Jamfile to fail if the test generates a warning. This would necessarily mean adding compiler-specific switches to the command line, though.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2014-05-19 21:33:35 UTC  
> Url: https://github.com/boostorg/utility/pull/8#issuecomment-43561381  

Ok, there is already a nice discussion on the list.  
  
Of course I'll add the test to the Jamfile.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2014-05-31 14:43:45 UTC  
> Url: https://github.com/boostorg/utility/pull/8#issuecomment-44750021  

Another iteration. I used the name proposed by Rob Stewart. I also added the overloads for suppressing the unused-local-typedefs warnings. So this solution is quite elegant.  
  
I'm only not sure if the header should stay in boost/utilities/ or be moved to boost/

---

## Comment 4

> Username: awulkiew  
> Created_at: 2014-06-02 20:36:28 UTC  
> Url: https://github.com/boostorg/utility/pull/8#issuecomment-44887658  

Merged with Core

---
