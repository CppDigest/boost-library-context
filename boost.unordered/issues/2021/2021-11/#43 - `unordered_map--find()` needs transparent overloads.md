# #43 - `unordered_map::find()` needs transparent overloads [Closed]

> Username: cmazakas  
> Created at: 2021-11-19 19:46:00 UTC  
> Updated at: 2021-11-30 16:32:25 UTC  
> Closed at: 2021-11-30 16:32:25 UTC  
> Url: https://github.com/boostorg/unordered/issues/43  

https://github.com/boostorg/unordered/blob/c8abaf32ee803711d84f24028ee7e855e1346913/include/boost/unordered/unordered_map.hpp#L748-L756  
  
These seem at odds with what's in the STL found here: https://en.cppreference.com/w/cpp/container/unordered_map/find  
  
and http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2018/p0919r2.html as well.

---

## Comment 1

> Username: cmazakas  
> Created at: 2021-11-19 22:30:38 UTC  
> Url: https://github.com/boostorg/unordered/issues/43#issuecomment-974515473  

@pdimov @glenfe I'm curious what you guys think about this one.  
  
I saw that P0919 mention similar overloads but these don't seem to be what we got in C++20 so should they stay or should they go?

---

## Comment 2

> Username: pdimov  
> Created at: 2021-11-19 22:37:50 UTC  
> Url: https://github.com/boostorg/unordered/issues/43#issuecomment-974518346  

If someone uses these overloads, removing them will break his code. The job of the maintainer is not to break things, it's to fix things.

---

## Comment 3

> Username: cmazakas  
> Created at: 2021-11-19 22:45:17 UTC  
> Url: https://github.com/boostorg/unordered/issues/43#issuecomment-974521208  

No doubt.  
  
In this instance, we will keep the existing overloads as they are now but in the interest of keeping `unordered_*` API-compatible with the STL, I'll instead add these extra `find` overloads.  
  
I suppose theoretically we could add defaulted template parameters and also defaulted function arguments to maintain API compatibility but I'd prefer to stay away from defaulting as it'd create a member function with a different arity than what's the in the STL.

---

## Comment 4

> Username: glenfe  
> Created at: 2021-11-19 23:27:55 UTC  
> Url: https://github.com/boostorg/unordered/issues/43#issuecomment-974537912  

There's nothing wrong with `boost::unordered_map` having extra functionality that isn't in `std::unordered_map`.  I wouldn't remove those, unless we find they are in some way broken or dangerous or useless.   
  
As for `boost::unordered_map` missing overloads of find that `std::unordered_map` provides, e.g. for heterogeneous lookup, that's another matter. These would be good to have.
