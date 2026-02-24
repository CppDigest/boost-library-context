# #5 use std::string as a container for char [Merged]

> Username: redbaron  
> Created at: 2014-03-19 19:45:54 UTC  
> Updated at: 2014-06-22 05:10:50 UTC  
> Merged at: 2014-04-01 02:53:55 UTC  
> Closed at: 2014-04-01 02:53:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/5  

That is what normally users would pass, so in some situations it  
can avoid copying data.

---

## Comment 1

> Username: redbaron  
> Created_at: 2014-03-20 00:49:25 UTC  
> Url: https://github.com/boostorg/spirit/pull/5#issuecomment-38124991  

TL;DR don't merge it yet, it introduces unexpected change in behaviour  
  
traits::move_to has move inconsistent semantic depending on participating types:  
- same type always result in dest being replaced with src  
- different types of src and dest can lead to situation where dest is appended with content of src.  
  
The second case makes "move_to" name to be very misleading and it's inconsistency make it hard to reason about. Simply changing it to "always update dest", breaks following code:  
  
``` cpp  
        BOOST_TEST(test_attr("foobar", string("foo") >> string("bar"), s));  
        BOOST_TEST(s == "foobar");  
```  
  
So, until it is solved can't go further with this PR

---

## Comment 2

> Username: djowel  
> Created_at: 2014-04-01 02:37:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/5#issuecomment-39165336  

is this just a matter of naming? maybe move_to should be named 'transfer' where move is just an optimization; and the src is always guaranteed to be going away (destructed), so we can do anything about the src. This is the motivation here.

---

## Comment 3

> Username: redbaron  
> Created_at: 2014-04-01 07:41:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/5#issuecomment-39178444  

It's not about the naming, it's about the fact that if Dest and Src are different then move_to can (I don't say always because I didn't check that) append Src to Dest, while if they are same then move_to always replaces dest with Src. So semantic of this simple utility function changes significantly. I should have closed this PR before it is merged, because now one particullar test case become broken.

---

## Comment 4

> Username: redbaron  
> Created_at: 2014-04-01 08:28:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/5#issuecomment-39181485  

By Src and Dest I mean move_to template arguments, not move_to function arguments.

---
