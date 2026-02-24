# #55 Develop mode type checking [Closed]

> Username: frenchtoast747  
> Created at: 2015-01-31 17:52:47 UTC  
> Updated at: 2021-10-02 22:20:17 UTC  
> Closed at: 2015-09-17 06:55:17 UTC  
> Url: https://github.com/boostorg/build/pull/55  

These changes change the core engine by loading the python modules in optimization mode by default and allow specifying a flag to disable optimized mode. When optimized mode is on, all `assert` statements as well as `if __debug__` statements are completely removed from the AST before being compiled to bytecode. Both are being used within the `bjam_signature` decorator and a majority of the code within the `build` package.  
  
I am still in the process of porting our large library written in Jam over to Python. I do my best to try and determine the signature of a function/rule based on the usage of a parameter on the Jam side, however, I've been bitten by the same  bug where I've passed in a string when it has expected a list.  
  
After completing the type checking, I've found several other type-bugs which has helped me solve a lot of problems I've been having.  
  
This addresses #54.

---

## Comment 1

> Username: frenchtoast747  
> Created_at: 2015-02-09 17:27:13 UTC  
> Updated_at: 2015-02-09 17:31:55 UTC  
> Url: https://github.com/boostorg/build/pull/55#issuecomment-73550764  

I've started to have second thoughts about the various type checking `assert` statements.  
  
Rather than having:  
  
```  
assert is_iterable_typed(property_set, basestring)  
```  
  
It should probably be something like:  
  
```  
assert is_property_set_literal(properties)  
```  
  
Or even:  
  
```  
assert is_property_set(properties)  
```  
  
So that if the majority of the python infrastructure is changed to be passing instances of property sets instead of the string literals, only the assert function will need to be changed.  
  
Property sets were just an example, there would be a different checking function for features, properties, property sets, etc.

---

## Comment 2

> Username: vprus  
> Created_at: 2015-04-13 09:37:59 UTC  
> Url: https://github.com/boostorg/build/pull/55#issuecomment-92292824  

Aaron,  
  
sorry it took me a while to get to this. Do you want me to review this version, or there's a better one elsewhere?

---

## Comment 3

> Username: frenchtoast747  
> Created_at: 2015-04-13 20:46:17 UTC  
> Url: https://github.com/boostorg/build/pull/55#issuecomment-92493479  

This is all I have at the moment. I'd appreciate it if you could review this version. Thanks!

---

## Comment 4

> Username: frenchtoast747  
> Created_at: 2015-04-13 21:29:29 UTC  
> Url: https://github.com/boostorg/build/pull/55#issuecomment-92506282  

Also, no worries! I've been using it on a personal branch which has been working just fine. Let me know if I should rebase this on top of the `develop` branch.

---

## Comment 5

> Username: frenchtoast747  
> Created_at: 2015-05-04 20:10:28 UTC  
> Url: https://github.com/boostorg/build/pull/55#issuecomment-98835527  

I have used `__debug__` when a specific type doesn't exist in a module and cannot be imported at the top of the module due to circular dependencies and when that type doesn't need to be imported during normal operation of that function. For example, there are quite a few instances of that where the `feature` module needs access to the `PropertySet` class.   
  
The last usage is within `util` specifically for the `bjam_signature` function since `bjam_signature` is needed for normal operation, but the type checking is not. Any of the helper functions used by the `__debug__` section in `bjam_signature` have also been guarded by a `__debug__`.  
  
I've left some of the helper functions outside of the `__debug__`, such as `is_iterable` and `is_iterable_typed`, so that they can be imported at the top of the modules and will not throw an error when run in optimized mode.

---

## Comment 6

> Username: vprus  
> Created_at: 2015-09-10 19:01:22 UTC  
> Url: https://github.com/boostorg/build/pull/55#issuecomment-139345709  

Aaron, if you rebase this change I'll be happy to apply it.

---

## Comment 7

> Username: frenchtoast747  
> Created_at: 2015-09-15 15:36:52 UTC  
> Url: https://github.com/boostorg/build/pull/55#issuecomment-140434774  

I have squash-rebased the typechecking branch on develop.  
  
Just curious, is there a way to run the tests against the Python port? I've been using my in house testing for that.

---

## Comment 8

> Username: vprus  
> Created_at: 2015-09-17 06:55:17 UTC  
> Url: https://github.com/boostorg/build/pull/55#issuecomment-140986475  

I've cherry-picked this change, thanks!  
  
If you're on Linux, testing Python port is basically `python test_all.py --python`, it's fairly healthy I believe. On Windows, most tests fail due to added "threading=multi" in the target paths, not yet sure why.

---

## Comment 9

> Username: frenchtoast747  
> Created_at: 2015-09-17 15:01:06 UTC  
> Url: https://github.com/boostorg/build/pull/55#issuecomment-141114304  

I have written tests that run our codebase with both the Jam and the Python versions to ensure that our ported Python produces the same output as the existing Jam and I've noticed the difference with "threading-multi". If I ever figure out the cause, I'll be sure to submit a patch.

---
