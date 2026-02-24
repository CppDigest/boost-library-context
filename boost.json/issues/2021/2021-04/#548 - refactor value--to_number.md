# #548 - refactor value::to_number [Closed]

> Username: vinniefalco  
> Created at: 2021-04-27 01:59:21 UTC  
> Updated at: 2021-05-08 12:29:10 UTC  
> Closed at: 2021-05-08 12:29:10 UTC  
> Url: https://github.com/boostorg/json/issues/548  

We can add a set of private member functions which return the error code enum instead, e.g.:  
```  
    template<class T>  
    T value::to_number_impl( error& e ) const noexcept ->  
        typename std::enable_if<  
            std::is_signed<T>::value &&  
            ! std::is_floating_point<T>::value,  
                T>::type;  
```  
  
And then re-implement the public overloads of `to_number` in terms of this.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-04-27 02:09:23 UTC  
> Url: https://github.com/boostorg/json/issues/548#issuecomment-827258504  

The goal is to decrease the footprint of the function (e.g. https://godbolt.org/z/cWxsf8TdY)  
  
If the error is communicated in `json::error` and an overload of `detail::throw_system_error` is added that takes `json::error` instead of `error_code`, a lot of the error handling paths in the generated code will be simplified because they'll no longer need to repeatedly construct an `error_code`.

---

## Comment 2

> Username: pdimov  
> Created at: 2021-05-07 01:18:54 UTC  
> Url: https://github.com/boostorg/json/issues/548#issuecomment-833988845  

E.g. https://godbolt.org/z/vMhTGb6T4
