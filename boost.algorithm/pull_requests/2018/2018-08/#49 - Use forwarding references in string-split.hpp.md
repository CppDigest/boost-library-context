# #49 Use forwarding references in string/split.hpp [Merged]

> Username: BenHetherington  
> Created at: 2018-08-01 20:08:10 UTC  
> Updated at: 2019-07-03 14:54:40 UTC  
> Merged at: 2019-07-03 14:54:40 UTC  
> Closed at: 2019-07-03 14:54:40 UTC  
> Url: https://github.com/boostorg/algorithm/pull/49  

On compilers that support C++11, this allows both lvalues and rvalues to be used as inputs to the `split()`, `find_all()`, and `ifind_all()` functions.  
  
For example, given a function `get_string()` that returns a `std::string`, this allows you to write:  
```cpp  
boost::split(result, get_string(), boost::is_any_of(" "));  
```  
  
I'm somewhat surprised that this hasn't been done before, so if I'm missing something, then do let me know. I've hardly done a comprehensive investigation on where else these changes could be made in `boost::algorithm`, so I'm also happy to expand this PR if necessary.

---

## Comment 1

> Username: mclow  
> Created_at: 2018-08-01 20:47:54 UTC  
> Url: https://github.com/boostorg/algorithm/pull/49#issuecomment-409717353  

Tests?

---

## Comment 2

> Username: BenHetherington  
> Created_at: 2018-08-01 21:42:23 UTC  
> Url: https://github.com/boostorg/algorithm/pull/49#issuecomment-409733780  

Whoops, looks like I saw the `tests/` directory, but missed the `string/tests/` directory, so I naïvely assumed there weren't any existing tests for `split` and co. I'll push some new lvalue tests now.

---

## Comment 3

> Username: BenHetherington  
> Created_at: 2018-10-03 14:55:12 UTC  
> Url: https://github.com/boostorg/algorithm/pull/49#issuecomment-426668150  

Now that tests have been added, is there anything else you'd like me to do to advance this PR, @mclow?

---

## Comment 4

> Username: BenHetherington  
> Created_at: 2019-06-10 12:21:31 UTC  
> Updated_at: 2019-06-10 12:22:05 UTC  
> Url: https://github.com/boostorg/algorithm/pull/49#issuecomment-500396623  

Sorry to bump this again, but it'd be really nice if this usability improvement could be merged at some point 🙂 If there's anything you'd like me to do to aid that, e.g. seeing where else this change could be helpful in `boost::algorithm`, or rebasing/squashing commits, then I'd be very happy to do so!

---

## Comment 5

> Username: mclow  
> Created_at: 2019-06-22 03:58:27 UTC  
> Url: https://github.com/boostorg/algorithm/pull/49#issuecomment-504625392  

Just saw this tonight. I'll see if I can get to it this weekend.

---
