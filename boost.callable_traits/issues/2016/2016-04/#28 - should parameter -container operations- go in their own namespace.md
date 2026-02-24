# #28 - should parameter "container operations" go in their own namespace? [Closed]

> Username: badair  
> Created at: 2016-04-17 19:19:42 UTC  
> Updated at: 2016-12-11 07:42:25 UTC  
> Closed at: 2016-12-11 07:42:25 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/28  

Reasons I'm asking:  
- names like `push_arg_front` don't imply that multiple types can be pushed (they can)  
- names like `push_args_front` put too much emphasis on the fact that multiple types can be pushed  
- `callable_traits::push_front` is too generic  
- `callable_traits::parms::push_front` or something like that might be nice.  
  
Thoughts?

---

## Comment 1

> Username: ricejasonf  
> Created at: 2016-06-23 19:09:08 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/28#issuecomment-228153266  

Just my cursory viewpoint.. I would say yes but make it `params` or maybe even `args`. The `args` function could be something like `to_tuple` or just `get`.

---

## Comment 2

> Username: badair  
> Created at: 2016-06-30 00:53:06 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/28#issuecomment-229531571  

@ricejasonf Thanks for the input. I agree that `params` would be good. Right now I'm preparing a PR for a Boost.TypeTraits fork with all of the CallableTraits features integrated. This will be a good thing for me to ask on the mailing list.

---

## Comment 3

> Username: badair  
> Created at: 2016-12-11 07:42:24 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/28#issuecomment-266268298  

Won't do for now
