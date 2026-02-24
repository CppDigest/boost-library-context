# #31 - Type aliases are missing description and more [Open]

> Username: vinniefalco  
> Created at: 2018-12-12 20:09:38 UTC  
> Updated at: 2021-01-25 23:39:29 UTC  
> Url: https://github.com/boostorg/docca/issues/31  

Type aliases are not showing the description and parameters:  
```  
/** Determine if `T` meets the requirements of @b CompletionHandler.  
  
    This trait checks whether a type meets the requirements for a completion  
    handler, and is also callable with the specified signature.  
    Metafunctions are used to perform compile time checking of template  
    types. This type will be `std::true_type` if `T` meets the requirements,  
    else the type will be `std::false_type`.   
  
    @par Example  
  
    Use with `static_assert`:  
  
    @code  
    struct handler  
    {  
        void operator()(error_code&);  
    };  
  
    static_assert(is_completion_handler<handler, void(error_code&)>::value,  
        "Not a completion handler");  
    @endcode  
*/  
template<class T, class Signature>  
using is_completion_handler = std::integral_constant<bool, __see_below__>;  
```  
  
Is only showing the brief and synopsis

---

## Comment 1

> Username: evanlenz  
> Created at: 2021-01-09 07:24:31 UTC  
> Url: https://github.com/boostorg/docca/issues/31#issuecomment-757110154  

Perhaps this one is obsolete too, although I can't be sure of the context where the problem occurred. I created a standalone test case with the above code at the C++ namespace level, and it produced the following result, which seems correct:  
  
![image](https://user-images.githubusercontent.com/604974/104085983-9badb280-5208-11eb-8173-bddb156ac11d.png)

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-01-25 23:38:54 UTC  
> Url: https://github.com/boostorg/docca/issues/31#issuecomment-767182674  

It would be nice if `__see_below__` was expanded as a quickbook macro into _see-below_ (note italics)   
![image](https://user-images.githubusercontent.com/1503976/105779532-6cdf4e00-5f23-11eb-9ef8-7b424f527b92.png)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-01-25 23:39:29 UTC  
> Url: https://github.com/boostorg/docca/issues/31#issuecomment-767182904  

For type aliases we want to see `T` and `CompletionHandler` as template params. But this might be a limitation of Doxygen's output.
