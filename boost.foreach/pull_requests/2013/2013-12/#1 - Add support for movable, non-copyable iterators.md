# #1 Add support for movable, non-copyable iterators. [Closed]

> Username: LEW21  
> Created at: 2013-12-18 00:04:58 UTC  
> Updated at: 2013-12-20 17:27:06 UTC  
> Closed at: 2013-12-20 16:03:07 UTC  
> Url: https://github.com/boostorg/foreach/pull/1  

This way it's possible to use BOOST_FOREACH with move-only iterators.

---

## Comment 1

> Username: ericniebler  
> Created_at: 2013-12-20 16:03:06 UTC  
> Url: https://github.com/boostorg/foreach/pull/1#issuecomment-31019765  

Iterators are required to be CopyConstuctible and CopyAssignable. If coroutine's iterators are not, then they are not iterators.

---

## Comment 2

> Username: LEW21  
> Created_at: 2013-12-20 16:58:12 UTC  
> Url: https://github.com/boostorg/foreach/pull/1#issuecomment-31024095  

But... C++11 range-based for works on them perfectly. And BOOST_FOREACH is also able to do so - with this trivial patch. There are no technical reasons to require copying here.

---

## Comment 3

> Username: ericniebler  
> Created_at: 2013-12-20 17:09:09 UTC  
> Url: https://github.com/boostorg/foreach/pull/1#issuecomment-31024959  

All the more reason not to change boost_foreach: just use C++11 range-based for. Boost_foreach is legacy code for C++03 users. It's been frozen for years. It's used everywhere. I am extremely reluctant to make any changes that has the potential to break code on all the ancient compilers it's been tested with and frequently used on.  
  
I would much rather see coroutine's iterators be made to conform to the standard concept requirement for iterators.

---

## Comment 4

> Username: LEW21  
> Created_at: 2013-12-20 17:21:37 UTC  
> Url: https://github.com/boostorg/foreach/pull/1#issuecomment-31025871  

Coroutine's iterators are currently copyable, but..  
  
When copied, they still refer to the same coroutine. And when you do:  
  
``` c++  
auto it2 = it;  
++it;  
++it2;  
```  
  
*it is not equal to *it2. *it2 is the element _after_ *it.  
  
Probably nothing in the standard says that this behavior is illegal, but it's quite unexpected, and breaking common assumptions about iterators.  
  
And it's impossible to support copying correctly, as we would need to copy the coroutine's stack.

---

## Comment 5

> Username: ericniebler  
> Created_at: 2013-12-20 17:27:06 UTC  
> Url: https://github.com/boostorg/foreach/pull/1#issuecomment-31026279  

You are describing an InputIterator. Another good example of an InputIterator is std::istream_iterator. The standard is very specific about the syntax and semantics the different refinements of the iterator concepts, and which algorithms work with which refinements. Please read it.

---
