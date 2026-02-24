# #54 - Type Checking [Closed]

> Username: frenchtoast747  
> Created at: 2015-01-22 16:17:55 UTC  
> Updated at: 2015-12-14 18:46:21 UTC  
> Closed at: 2015-12-14 07:34:49 UTC  
> Url: https://github.com/boostorg/build/issues/54  

I'm continuously working on and using the Python port and I've been bitten by a similar bug several times: I pass in a string when the function is expecting a list. The function will usually continue on as normal and later on show some cryptic error message about how it can't find some target named '_', 'L', or any other various first letters of one of my targets.  
  
Some of the core boost-build functions and methods already contain asserts for type checking, but there are a lot that don't. I've developed somewhat of a solution that utilizes `bjam_signature`. It uses the signature passed in to determine the appropriate type of a parameter and raises a `TypeError` if, say, a string is passed in where a list was expected.  
  
I've already tried implementing this solution and it works. However, there are some functions that require a list as a parameter and account for parameters that might be passed a string. For example, `feature.compose`:  
  
```  
# feature.py  
@bjam_signature((["composite_property_s"], ["component_properties_s", "*"]))  
def compose (composite_property_s, component_properties_s):  
    # ...  
    component_properties_s = to_seq (component_properties_s)  
```  
  
And in `type.register`, `feature.compose` is called with a string instead of a list:  
  
```  
if base_type:  
        feature.compose ('<target-type>' + type, replace_grist (base_type, '<base-target-type>'))  
        feature.compose ('<base-target-type>' + type, '<base-target-type>' + base_type)  
```  
  
My solution doesn't work when variables are allowed to be either a list or a string even though the bjam signature says it should be a list. My question: would it be permissible to enforce this strict(er) typing by implementing the `bjam_signature` type check and removing all type coercions in the core? Or should I try the next idea:  
  
Instead of or along with utilizing the `bjam_signature` type check, it would be nice to place type checking asserts for each function/method that cannot support a `bjam_signature`.  
  
Both solutions would require a small change to the engine code to enable the the `PYTHONOPTMIZE` flag by default (so all asserts and `if __debug__` blocks are removed) and allow the optimization flag to be disabled by another flag for development/debugging purposes.

---

## Comment 1

> Username: vprus  
> Created at: 2015-01-27 09:32:34 UTC  
> Url: https://github.com/boostorg/build/issues/54#issuecomment-71615835  

Aaron,  
  
personally I think that stricter checking would be fine. E.g. passing a list as the second parameter to feature.compose seems quite reasonable to me.

---

## Comment 2

> Username: frenchtoast747  
> Created at: 2015-01-27 14:53:10 UTC  
> Url: https://github.com/boostorg/build/issues/54#issuecomment-71659972  

Thanks Vladimir! I'll implement my solution and create a pull request soon.

---

## Comment 3

> Username: vprus  
> Created at: 2015-12-14 07:34:49 UTC  
> Url: https://github.com/boostorg/build/issues/54#issuecomment-164368909  

Since the pull request was merged quite some time ago, I assume the issue is also resolved.

---

## Comment 4

> Username: frenchtoast747  
> Created at: 2015-12-14 18:46:21 UTC  
> Url: https://github.com/boostorg/build/issues/54#issuecomment-164522893  

Yes it is. Thanks!
