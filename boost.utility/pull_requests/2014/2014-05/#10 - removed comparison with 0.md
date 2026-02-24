# #10 removed comparison with 0 [Closed]

> Username: akrzemi1  
> Created at: 2014-05-29 15:42:18 UTC  
> Updated at: 2014-05-31 18:25:41 UTC  
> Closed at: 2014-05-31 18:25:41 UTC  
> Url: https://github.com/boostorg/utility/pull/10  

The concept is supposed to generalize pointers and optional<>, but the latter has abandoned the comparison with 0 a long while ago.

---

## Comment 1

> Username: ericniebler  
> Created_at: 2014-05-29 17:33:57 UTC  
> Url: https://github.com/boostorg/utility/pull/10#issuecomment-44559968  

I'm unfamiliar with `boost::optional`. Is there also a comparison with `std::nullptr_t` (on C++11 compilers)? Should that be part of the documentation?

---

## Comment 2

> Username: ericniebler  
> Created_at: 2014-05-29 17:34:49 UTC  
> Url: https://github.com/boostorg/utility/pull/10#issuecomment-44560072  

Oh, shouldn't you be merging to the develop branch instead of master? Everything should go through develop first, IMO.

---

## Comment 3

> Username: akrzemi1  
> Created_at: 2014-05-30 07:47:21 UTC  
> Url: https://github.com/boostorg/utility/pull/10#issuecomment-44623680  

Regarding comparison with `nullptr`, a similar discussion occurred in connection with `std::experimental::optional` and it was explicitly desired that such conversion/comparison does not work. See the summarized rationale here: http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2012/n3406.html#rationale.no_nullptr. In short, '`nullptr`' indicates that you are dealing with a pointer, which `optional` is not.

---

## Comment 4

> Username: akrzemi1  
> Created_at: 2014-05-30 07:48:42 UTC  
> Url: https://github.com/boostorg/utility/pull/10#issuecomment-44623775  

Yes, I should have made a request for develop. Sorry, I am new to Git and GitHub. Do I need to make another request, or can you just merge it to develop?

---

## Comment 5

> Username: ericniebler  
> Created_at: 2014-05-30 20:06:17 UTC  
> Url: https://github.com/boostorg/utility/pull/10#issuecomment-44693805  

Not through the github interface, but I think I can merge this to develop from the command line. I'll let you know.

---

## Comment 6

> Username: ericniebler  
> Created_at: 2014-05-31 18:25:41 UTC  
> Url: https://github.com/boostorg/utility/pull/10#issuecomment-44755670  

Merged into develop.

---
