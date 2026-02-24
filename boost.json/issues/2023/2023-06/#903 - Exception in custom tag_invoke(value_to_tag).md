# #903 - Exception in custom tag_invoke(value_to_tag) [Closed]

> Username: RoyBellingan  
> Created at: 2023-06-04 15:22:57 UTC  
> Updated at: 2024-10-23 12:43:24 UTC  
> Closed at: 2023-07-05 14:35:26 UTC  
> Url: https://github.com/boostorg/json/issues/903  

### Version of Boost  
1_82  
  
For example here (but this code pattern is common in other places)  
  
https://github.com/boostorg/json/blob/be759c5051b828f77b59d5c67b9b53d7330e627e/include/boost/json/detail/value_to.hpp#LL652C44-L652C54  
  
In case a custom tag_invoke throw the information on why and where is either lost or severely reduced (just the error.code() is passed in the chain) like here https://github.com/boostorg/json/blob/be759c5051b828f77b59d5c67b9b53d7330e627e/include/boost/json/detail/value_to.hpp#LL652C44-L652C54  
  
I think it can make sense to allow (like is happening for std::bad_alloc ) to leave certain exception to be re thrown.

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-06-04 16:04:02 UTC  
> Url: https://github.com/boostorg/json/issues/903#issuecomment-1575623078  

Throwing overloads of conversion functions should allow exceptions to pass through. Do you have a specific example when that doesn't happen?

---

## Comment 2

> Username: RoyBellingan  
> Created at: 2023-06-04 18:01:50 UTC  
> Updated at: 2023-06-04 20:05:14 UTC  
> Url: https://github.com/boostorg/json/issues/903#issuecomment-1575658190  

Well the value_to_impl linked above has  
```  
    catch( ... )  
    {  
        error_code ec;  
        BOOST_JSON_FAIL(ec, error::exception);  
        return {boost::system::in_place_error, ec};  
    }  
 ```  
That will intercept all of them.  
  
For example here https://godbolt.org/z/767674j5c  
The result I got is   
```  
terminate called after throwing an instance of 'boost::detail::with_throw_location<boost::system::system_error>'  
  what():  got exception [boost.json:15 at /opt/compiler-explorer/libs/boost_1_82_0/boost/json/detail/value_to.hpp:671:9 in function 'typename std::enable_if<(! typename boost::mp11::detail::mp_valid_impl<boost::json::detail::has_nonthrowing_user_conversion_to_impl, T>::type::value), boost::system::result<T> >::type boost::json::detail::value_to_impl(boost::json::try_value_to_tag<T>, const boost::json::value&, user_conversion_tag)']  
```  
  
So the exception is "internally" captured, and not propagated.

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-06-04 20:36:56 UTC  
> Url: https://github.com/boostorg/json/issues/903#issuecomment-1575703545  

You are right, I somehow missed that. I will look into ways to fix that.

---

## Comment 4

> Username: grisumbras  
> Created at: 2023-07-03 15:39:36 UTC  
> Url: https://github.com/boostorg/json/issues/903#issuecomment-1618709880  

@RoyBellingan can you check if #913 fixes your problem?

---

## Comment 5

> Username: RoyBellingan  
> Created at: 2023-07-03 15:41:11 UTC  
> Url: https://github.com/boostorg/json/issues/903#issuecomment-1618716055  

Ciao @grisumbras I will try soon, thank you!

---

## Comment 6

> Username: grisumbras  
> Created at: 2024-10-23 12:43:23 UTC  
> Url: https://github.com/boostorg/json/issues/903#issuecomment-2432030586  

I reversed #913 in #1041, because it has caused other issues and the approach was overall pretty unwieldy. Instead we'll just have to require tag_invoke overloads to throw `std/boost::system::system_error`.
