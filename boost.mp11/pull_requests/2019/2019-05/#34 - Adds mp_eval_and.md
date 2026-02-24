# #34 Adds mp_eval_and [Closed]

> Username: HDembinski  
> Created at: 2019-05-15 06:30:17 UTC  
> Updated at: 2019-05-30 21:24:20 UTC  
> Closed at: 2019-05-30 21:24:20 UTC  
> Url: https://github.com/boostorg/mp11/pull/34  

See #33. I think this is a useful primitive to have in the library. Perhaps the implementation can be optimized and the test code needs a better test case, as pointed out in the issue.

---

## Comment 1

> Username: HDembinski  
> Created_at: 2019-05-15 06:32:49 UTC  
> Url: https://github.com/boostorg/mp11/pull/34#issuecomment-492522880  

I put the code in `function.hpp`, since I saw it as a form of `mp_and`, but maybe it is closer to `mp_eval_or` and should reside in `utility.hpp` with `mp_eval_or`.

---

## Comment 2

> Username: HDembinski  
> Created_at: 2019-05-30 21:24:20 UTC  
> Url: https://github.com/boostorg/mp11/pull/34#issuecomment-497491534  

It seems this meta function is useless after all, because traits of the is_X kind do not cause substitution failures. I am closing this.

---
