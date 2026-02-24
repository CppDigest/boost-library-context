# #18 Remove std::*nary_function [Closed]

> Username: kundor  
> Created at: 2016-05-22 01:36:27 UTC  
> Updated at: 2017-05-17 20:02:57 UTC  
> Closed at: 2017-05-17 20:02:57 UTC  
> Url: https://github.com/boostorg/algorithm/pull/18  

You asked me to remind you about PR #17 / commit 3aef0ab so you could merge to master.  
  
Also, in C++17, std::unary_function and std::binary_function will be removed from the standard, so we should also remove their uses.  
  
I brought this up on the mailing list: [What to do about std::binary_function and std::unary_function?](http://lists.boost.org/Archives/boost/2016/05/229402.php)  
There was not a lot of response, but the slight preponderance of opinion seemed to be to just remove the inheritance: everything should keep working, but maybe provide a typedef for result_type for use with boost::bind.  
  
I did that. I didn't add the typedef for the structs in `detail` since they are "internal."

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-03-22 09:50:00 UTC  
> Url: https://github.com/boostorg/algorithm/pull/18#issuecomment-288348252  

This seems to be fixed in develop. I think this PR can be closed.

---

## Comment 2

> Username: mclow  
> Created_at: 2017-03-22 17:22:49 UTC  
> Url: https://github.com/boostorg/algorithm/pull/18#issuecomment-288474485  

I'll merge to master after the beta.

---

## Comment 3

> Username: Lastique  
> Created_at: 2017-03-22 17:33:05 UTC  
> Url: https://github.com/boostorg/algorithm/pull/18#issuecomment-288477772  

When you do, please, have a look at https://github.com/boostorg/algorithm/pull/34, which may make it easier for you. Or you can just close that PR if you don't need it.

---

## Comment 4

> Username: mclow  
> Created_at: 2017-05-17 20:02:56 UTC  
> Url: https://github.com/boostorg/algorithm/pull/18#issuecomment-302215750  

This is all done in develop. Closing.

---
