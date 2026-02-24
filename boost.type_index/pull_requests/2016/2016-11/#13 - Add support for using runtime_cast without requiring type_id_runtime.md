# #13 Add support for using runtime_cast without requiring type_id_runtime. [Merged]

> Username: cdglove  
> Created at: 2016-11-05 20:47:10 UTC  
> Updated at: 2017-02-28 01:33:40 UTC  
> Merged at: 2017-02-28 01:33:08 UTC  
> Closed at: 2017-02-28 01:33:08 UTC  
> Url: https://github.com/boostorg/type_index/pull/13  

Add BOOST_TYPE_INDEX_IMPLEMENT_RUNTIME_CAST, which implements the functionality for runtime_cast to function, but does not include the BOOST_TYPE_INDEX_REGISTER_CLASS macro.  
  
Reimplement BOOST_TYPE_INDEX_REGISTER_RUNTIME_CLASS to simply call BOOST_TYPE_INDEX_IMPLEMENT_RUNTIME_CAST and BOOST_TYPE_INDEX_REGISTER_CLASS.  
  
This allows users to opt-in to one or the other, instead of forcing them to generate both virtual functions.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2017-01-08 11:44:43 UTC  
> Url: https://github.com/boostorg/type_index/pull/13#issuecomment-271145904  

Looks good. Could you please add more tests on the new macro?

---

## Comment 2

> Username: apolukhin  
> Created_at: 2017-02-08 19:22:48 UTC  
> Url: https://github.com/boostorg/type_index/pull/13#issuecomment-278433489  

@cdglove  ping :)

---

## Comment 3

> Username: cdglove  
> Created_at: 2017-02-08 19:55:36 UTC  
> Url: https://github.com/boostorg/type_index/pull/13#issuecomment-278442852  

Sorry, been busy! Yes, I will add more tests this weekend and update the PR.

---

## Comment 4

> Username: apolukhin  
> Created_at: 2017-02-28 01:33:40 UTC  
> Url: https://github.com/boostorg/type_index/pull/13#issuecomment-282913721  

Cool feature! Thanks!

---
