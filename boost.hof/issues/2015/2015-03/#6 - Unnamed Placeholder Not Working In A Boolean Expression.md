# #6 - Unnamed Placeholder Not Working In A Boolean Expression [Closed]

> Username: therealgymmy  
> Created at: 2015-03-17 18:00:53 UTC  
> Updated at: 2015-03-17 20:51:58 UTC  
> Closed at: 2015-03-17 20:51:58 UTC  
> Url: https://github.com/boostorg/hof/issues/6  

Hi, when I tried the unnamed placeholder in your library in the following expression:  
  
```  
auto comp = _ > 1;  
assert(comp(2) == true);  
assert(comp(1) == false);  
```  
  
I get `false` for both the comparisons.  
  
Replacing `_` with the normal placeholder `_1` would return true for comp(2) as intended. Expressions that produce a value such as `_ + _` and `_ * 10` also work as intended.  
  
I looked into the placeholder code, but couldn't really figure out why the boolean expression didn't work as intended. Could you please take a look?

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-03-17 18:34:45 UTC  
> Url: https://github.com/boostorg/hof/issues/6#issuecomment-82515079  

Ok looking at the code, I think I see whats wrong. I reverse the parameters when I do partial application(the usage of `left` and `right` is backwards [here](https://github.com/pfultz2/Fit/blob/master/fit/placeholders.h#L284)). Thats why it works for plus and multiply because they are communative.   
  
I'll try to update it and add test cases for this as well later today.

---

## Comment 2

> Username: therealgymmy  
> Created at: 2015-03-17 19:27:48 UTC  
> Url: https://github.com/boostorg/hof/issues/6#issuecomment-82555085  

I tried reversing `left` and `right` and it worked as intended.  
Thanks! Looking forward to your fix.

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-03-17 20:51:58 UTC  
> Url: https://github.com/boostorg/hof/issues/6#issuecomment-82595454  

I merged in the fix into master.
