# #394 Fix missing namespace name [Merged]

> Username: tsnorri  
> Created at: 2024-11-08 10:47:06 UTC  
> Updated at: 2024-11-19 12:51:59 UTC  
> Merged at: 2024-11-19 12:51:59 UTC  
> Closed at: 2024-11-19 12:51:59 UTC  
> Url: https://github.com/boostorg/graph/pull/394  

For some reason when using `cycle_canceling` as part of a larger algorithm, Clang++19 reported that it could not find `cycle_canceling_dispatch1`. Compiling [the example](https://www.boost.org/doc/libs/1_86_0/libs/graph/example/cycle_canceling_example.cpp) with the same options did work. In any case I think the namespace name should be applied to the call.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2024-11-10 23:04:10 UTC  
> Url: https://github.com/boostorg/graph/pull/394#issuecomment-2466977569  

I agree, thanks for the fix!

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2024-11-10 23:38:14 UTC  
> Updated_at: 2024-11-10 23:38:29 UTC  
> Url: https://github.com/boostorg/graph/pull/394#issuecomment-2466988969  

Actually... although on the surface this change looks right, I was expecting the example to have something hacky in it to make it work, but it doesn't. So I would like to know why it doesn't work for you. Or, why it works for the example. It's just slightly too mysterious to leave unanswered.

---

## Comment 3

> Username: tsnorri  
> Created_at: 2024-11-11 07:41:17 UTC  
> Url: https://github.com/boostorg/graph/pull/394#issuecomment-2467439713  

I’ll see if I can find out the reason.

---

## Comment 4

> Username: tsnorri  
> Created_at: 2024-11-11 12:32:13 UTC  
> Url: https://github.com/boostorg/graph/pull/394#issuecomment-2468070561  

So I got the syntax tree from Clang and in the case of the example, `cycle_canceling_dispatch1` is found via ADL. The exact declaration of the function template specialisation is quite long but the passed `adjacency_list` has (indirectly) a template parameter whose type is `boost::detail::edge_desc_impl`. I think this causes `boost::detail` to be considered when the compiler looks for `cycle_canceling_dispatch1`.  
  
To me this doesn’t seem to be intentional.

---
