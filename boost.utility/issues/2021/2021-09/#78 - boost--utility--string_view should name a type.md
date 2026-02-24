# #78 - boost::utility::string_view should name a type [Open]

> Username: vinniefalco  
> Created at: 2021-09-28 18:29:17 UTC  
> Updated at: 2021-09-28 18:47:11 UTC  
> Url: https://github.com/boostorg/utility/issues/78  

if you include `<boost/utility/string_view.hpp>` then this line should work:  
  
```  
using string_view = boost::utility::string_view;  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2021-09-28 18:30:20 UTC  
> Url: https://github.com/boostorg/utility/issues/78#issuecomment-929517298  

Why?

---

## Comment 2

> Username: glenfe  
> Created at: 2021-09-28 18:31:02 UTC  
> Url: https://github.com/boostorg/utility/issues/78#issuecomment-929517753  

We don't use the `utility` namespace

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-09-28 18:36:33 UTC  
> Url: https://github.com/boostorg/utility/issues/78#issuecomment-929521734  

> Why?  
  
Because I can't be bothered to remember whether or not each of N Boost libraries "uses its namespace" or not. I include a header, I expect to find the symbol in its corresponding namespace. I thought the convention was that Boost.X has a namespace called X and its symbols go there. If the types are worthy they are also lifted into the boost namespace. Having random exceptions makes things harder to use.

---

## Comment 4

> Username: glenfe  
> Created at: 2021-09-28 18:38:17 UTC  
> Url: https://github.com/boostorg/utility/issues/78#issuecomment-929523050  

It's true for all new Boost libraries, but even (for example), `shared_ptr` is `boost::shared_ptr` not `boost::smart_ptr::shared_ptr`.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-09-28 18:39:01 UTC  
> Url: https://github.com/boostorg/utility/issues/78#issuecomment-929523561  

So all new boost libraries put their symbols in `boost::`? That's news to me. I'll change Beast in time for 1.78 then.

---

## Comment 6

> Username: glenfe  
> Created at: 2021-09-28 18:43:02 UTC  
> Url: https://github.com/boostorg/utility/issues/78#issuecomment-929526429  

No, requiring a library namespace under `boost` is true for all new libraries. But Utility, Smart_Ptr etc. and older libraries are not subject to that rule.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2021-09-28 18:46:49 UTC  
> Url: https://github.com/boostorg/utility/issues/78#issuecomment-929529090  

Well, that's my point - I have to remember which libraries follow the rule and which don't. I'd rather not.

---

## Comment 8

> Username: Lastique  
> Created at: 2021-09-28 18:47:11 UTC  
> Url: https://github.com/boostorg/utility/issues/78#issuecomment-929529343  

Libraries that focus on a given topic do use a dedicated namespace, and are supposed to in the future. Boost.Utility, Boost.Detail and to a degree Boost.Core are not such libraries - those are just a collection of random unrelated tools. Historically, those were introduced in namespace `boost`. We might consider moving some components to dedicated namespaces where it makes sense (e.g. Boost.SmartPtr). But having `boost::utility` in particular, doesn't make much sense, IMHO.  
  
That said, it may make sense to extract `string_view` and `string_ref` to a separate library and use a namespace in *that* library.
