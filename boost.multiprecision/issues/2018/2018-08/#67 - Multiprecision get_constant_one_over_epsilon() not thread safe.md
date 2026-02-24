# #67 - Multiprecision get_constant_one_over_epsilon() not thread safe [Closed]

> Username: mkorpar  
> Created at: 2018-08-16 09:36:46 UTC  
> Updated at: 2018-08-16 17:11:02 UTC  
> Closed at: 2018-08-16 17:10:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/67  

```  
template <class T>  
const T& get_constant_one_over_epsilon()  
{  
   static const bool is_init = false;  
   static T result;  
   if (is_init == false)  
   {  
      typedef typename mpl::front<typename T::unsigned_types>::type ui_type;  
      result = static_cast<ui_type>(1u);  
      eval_divide(result, std::numeric_limits<number<T> >::epsilon().backend());  
   }  
  
   constant_initializer<T, &get_constant_one_over_epsilon<T> >::do_nothing();  
  
   return result;  
}  
```  
  
is_init is never set to true.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-16 17:11:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/67#issuecomment-413618151  

Not sure what happened there, none of that initialization code was quite right.  Should all be consistent and fixed now.
