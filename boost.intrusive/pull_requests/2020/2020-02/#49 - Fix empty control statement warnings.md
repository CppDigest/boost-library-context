# #49 Fix empty control statement warnings [Merged]

> Username: SigmundVik  
> Created at: 2020-02-07 09:51:25 UTC  
> Updated at: 2020-10-19 11:26:24 UTC  
> Merged at: 2020-10-19 11:26:18 UTC  
> Closed at: 2020-10-19 11:26:18 UTC  
> Url: https://github.com/boostorg/intrusive/pull/49  

This change replaces code like this:  
```  
      if(safemode_or_autounlink)  
         BOOST_INTRUSIVE_SAFE_HOOK_DEFAULT_ASSERT(node_algorithms::unique(to_insert));  
```  
  
with:  
```  
      BOOST_INTRUSIVE_SAFE_HOOK_DEFAULT_ASSERT(!safemode_or_autounlink || node_algorithms::unique(to_insert));  
```  
  
The reason for this change is that the first form generates a warning for Visual C++ 14.1 in builds without asserts:  
```  
warning C4390: ';': empty controlled statement found; is this the intent?  
```  
  
Before this change the code was using a mix of the two forms when invoking the `BOOST_INTRUSIVE_SAFE_HOOK_DEFAULT_ASSERT()` and `BOOST_INTRUSIVE_INVARIANT_ASSERT()` macros.  Now it consistently uses the second form.

---

## Comment 1

> Username: SigmundVik  
> Created_at: 2020-02-07 09:53:29 UTC  
> Url: https://github.com/boostorg/intrusive/pull/49#issuecomment-583316200  

Please pay special attention to the `treap_node_extra_checker` call operator change.  If the `node_traits::get_left()` and `node_traits::get_right()` functions are allowed to have side effects, then we need to use curly braces to fix this case instead of moving the function calls inside the `BOOST_INTRUSIVE_INVARIANT_ASSERT()` macro invocation.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2020-10-19 11:26:24 UTC  
> Url: https://github.com/boostorg/intrusive/pull/49#issuecomment-712092483  

Many thanks for the patch, it looks good, as get_rigth/get_left() is not suppossd to have side-effects

---
