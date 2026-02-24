# #19 - tribool doesn't play well with auto [Open]

> Username: xiao-feng  
> Created at: 2019-11-11 16:38:38 UTC  
> Updated at: 2021-02-21 17:10:26 UTC  
> Url: https://github.com/boostorg/logic/issues/19  

This prints "Not true.":  
```  
  tribool b = indeterminate;  
  if(b) {  
      cout << boolalpha << b << endl;  
  }  
  else {  
      cout << "Not true." << endl;  
  }  
```  
This prints "indeterminate":  
```  
  auto b = indeterminate;  
  if(b) {  
      cout << boolalpha << b << endl;  
  }  
  else {  
      cout << "Not true." << endl;  
  }  
```  
Is it a bug?  Or is it expected behavior?  What's the rationale?  
  
Thanks!

---

## Comment 1

> Username: u8cat  
> Created at: 2021-02-21 17:10:26 UTC  
> Url: https://github.com/boostorg/logic/issues/19#issuecomment-782890477  

That's because `indeterminate` is a function:  
```cpp  
// Check if the given tribool has an indeterminate value. Also doubles as a  
// keyword for the 'indeterminate' value  
BOOST_CONSTEXPR inline bool indeterminate(tribool x, detail::indeterminate_t) BOOST_NOEXCEPT  
{  
  return x.value == tribool::indeterminate_value;  
}  
```  
Therefore, `b` is a pointer to the function, which will never be `nullptr`.
