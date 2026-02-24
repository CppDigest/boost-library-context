# #18 - Warnings in float_sort_test [Closed]

> Username: pdimov  
> Created at: 2017-11-14 00:03:47 UTC  
> Updated at: 2017-11-17 02:16:54 UTC  
> Closed at: 2017-11-17 02:16:54 UTC  
> Url: https://github.com/boostorg/sort/issues/18  

```  
test\float_sort_test.cpp(86): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
```  
```  
test\float_sort_test.cpp(119): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
```  
  
These are emitted by  
  
```  
  // Trying both positive and negative sorted and reverse sorted data.  
  base_vec.clear();  
  for (size_t i = 0; i < input_count; ++i) base_vec.push_back(-i);  
```  
  
The comment implies that negative values are supposed to be tested, but `-i` when `i` is `size_t` does not result in a negative number, but in a large positive one.  
  
Looks like `for (int i = 0; ...` is intended.

---

## Comment 1

> Username: spreadsort  
> Created at: 2017-11-17 02:16:54 UTC  
> Url: https://github.com/boostorg/sort/issues/18#issuecomment-345125278  

Thanks for the report!  It should be fixed now.
