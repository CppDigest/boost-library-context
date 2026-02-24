# #72 [strategies][agnostic] fix bug in Graham-Andrew strategy: [Merged]

> Username: mkaravel  
> Created at: 2014-06-20 11:01:01 UTC  
> Updated at: 2014-07-01 23:24:38 UTC  
> Merged at: 2014-06-30 18:51:11 UTC  
> Closed at: 2014-06-30 18:51:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/72  

last is used again after two calls to pop_back() which can invalidate  
the reference;  
patch: make last a true point rather than a reference  
bug reported/patch suggested by David Zhao  
  
(re-opening PR #70)

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-06-20 20:50:34 UTC  
> Updated_at: 2014-06-20 23:16:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/72#issuecomment-46725097  

Below the fixed fragment the following can be found:  
  
// Remove last two points from stack, and add last again  
// This is much faster then erasing the one but last.  
output.pop_back();  
output.pop_back();  
output.push_back(last);  
  
I wonder is calling erase() really slower? It should be more or less the same as an element copy and resize. Or as same as the proposal below.  
  
We could leave the reference but replace the code using the reference with:  
last2 = last; // could be boost/std::move  
output.pop_back();  
  
EDIT:  
But since the above solution would require defining non-const reference to Point maybe it would be more intuitive to just store iterators. It would then also be simpler to spot this kind of bugs:  
  
```  
rev_iterator rit = output.rbegin();  
rev_iterator back_it = rit++;  
rev_iterator prev_it = rit++;  
  
if (Factor * side::apply(*rit, *back_it, *prev_it) <= 0)  
{  
    *prev_it = *back_it;  
    output.pop_back();  
    output_size--;  
}  
```  
  
As an optimization fwd iterators could be used (reverse ones decrements the wrapped forward one before dereferencing it) so the compiler should optimize it and it should be as efficient as using references:  
  
```  
iterator it = --output.end();  
iterator back_it = it--;  
iterator prev_it = it--;  
```

---
