# #61 Implement ostream_joiner [Closed]

> Username: glenfe  
> Created at: 2019-12-12 03:52:49 UTC  
> Updated at: 2019-12-12 19:02:09 UTC  
> Closed at: 2019-12-12 19:02:08 UTC  
> Url: https://github.com/boostorg/utility/pull/61  



---

## Comment 1

> Username: Lastique  
> Created_at: 2019-12-12 09:48:17 UTC  
> Url: https://github.com/boostorg/utility/pull/61#issuecomment-564931221  

This might be better placed in Boost.Iterator.

---

## Comment 2

> Username: glenfe  
> Created_at: 2019-12-12 15:20:44 UTC  
> Url: https://github.com/boostorg/utility/pull/61#issuecomment-565051476  

Iterator is one obvious place for it. Utility has minimal dependencies though, and we have users that tend to favor those.

---

## Comment 3

> Username: Lastique  
> Created_at: 2019-12-12 15:34:48 UTC  
> Url: https://github.com/boostorg/utility/pull/61#issuecomment-565057687  

I really don't like the idea of us putting components wherever there are least dependencies. We could put it in Boost.Core in that case. This is a sure way to having a total mess in Boost.

---

## Comment 4

> Username: glenfe  
> Created_at: 2019-12-12 15:37:52 UTC  
> Url: https://github.com/boostorg/utility/pull/61#issuecomment-565059036  

Clearly that isn't the only criteria, to warrant "wherever". i.e. This single class template is as much a "utility" as, say, string_view.

---

## Comment 5

> Username: Lastique  
> Created_at: 2019-12-12 15:46:34 UTC  
> Url: https://github.com/boostorg/utility/pull/61#issuecomment-565062933  

Well, it is much closer to Iterator than to Utility. `std::ostream_joiner` is even defined in `<iterator>`  
  
If there are unnecessary dependencies in Boost.Iterator, maybe we could work on that.  
  
PS: I'm not quite happy about `string_view` and some other components either. I'd prefer not to make it worse.

---

## Comment 6

> Username: pdimov  
> Created_at: 2019-12-12 16:12:37 UTC  
> Url: https://github.com/boostorg/utility/pull/61#issuecomment-565074065  

Putting small things in Utility for dependency reasons isn't entirely wrong as a strategy. If we look at the standard library, `swap` was correctly moved to `<utility>`, and it would've been much better if `less` and `hash` were there too, instead of in `<functional>`.  
  
In fact one might argue that this approach works against Utility becoming the mess it was before we cleaned it up - if the thing depends on this and that, it probably doesn't belong here.

---

## Comment 7

> Username: Lastique  
> Created_at: 2019-12-12 17:28:05 UTC  
> Url: https://github.com/boostorg/utility/pull/61#issuecomment-565105129  

Standard library is a terrible example to follow, if you ask me. Its headers are so coarse-grained that people seriously consider rewriting portions of it or including its internal headers just to avoid including the proper headers. And this is not just a sign of inappropriate placement of components. Moving stuff around doesn't really help. I'm as reluctant to include `<utility>` just to get `swap` and `move` as I was to include `<algorithm>`, and I actually often prefer to replace `move` and `forward` with a `static_cast` for that reason.  
  
Having components at their right place is good for users, as they are more likely to find the components they are looking for. It is also good maintenance-wise. It does not affect compile-time dependencies, as you're not introducing new includes. It may affect submodule-wise dependencies, but given that we're still distributing monolithic Boost and it is also delivered to users as a monolithic tree, that argument is rather weak.

---

## Comment 8

> Username: pdimov  
> Created_at: 2019-12-12 17:37:14 UTC  
> Updated_at: 2019-12-12 17:38:35 UTC  
> Url: https://github.com/boostorg/utility/pull/61#issuecomment-565108604  

I'd recommend `io` for `ostream_joiner`, actually. That's exactly what `io` was for, but nobody remembers. Glen should just take over it and put all iostreams-related utilities (such as f.ex. ostream_string) there.

---

## Comment 9

> Username: glenfe  
> Created_at: 2019-12-12 19:02:08 UTC  
> Url: https://github.com/boostorg/utility/pull/61#issuecomment-565139580  

IO sounds good to me. Will do.

---
