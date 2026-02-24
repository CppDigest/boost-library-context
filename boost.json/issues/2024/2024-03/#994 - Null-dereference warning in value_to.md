# #994 - Null-dereference warning in value_to [Closed]

> Username: infn-ke  
> Created at: 2024-03-21 21:47:21 UTC  
> Updated at: 2024-10-09 13:56:50 UTC  
> Closed at: 2024-10-09 13:56:49 UTC  
> Url: https://github.com/boostorg/json/issues/994  

I'm using boost 1.83 and gcc13.2 and can see a gcc compiler warning about a potential null de-reference.   
  
```bash  
In function 'boost::json::result<T> boost::json::detail::value_to_impl(sequence_conversion_tag, boost::json::try_value_to_tag<T>, const boost::json::value&, const Ctx&) [with T = std::array<unsigned char, 128>; Ctx = std::tuple<allow_exceptions, no_context>]',  
    inlined from 'typename boost::json::result_for<T, boost::json::value>::type boost::json::try_value_to(const value&, const Context&) [with T = std::array<unsigned char, 128>; Context = std::tuple<detail::allow_exceptions, detail::no_context>]' at /usr/include/boost/json/value_to.hpp:230:33:  
/usr/include/boost/json/detail/value_to.hpp:315:16: warning: potential null pointer dereference [-Wnull-dereference]  
  315 |         *ins++ = std::move(*elem_res);  
```  
  
Is the check `elem_res.has_error()` insufficient to guarantee that no null-pointer de-reference will happen?

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-03-22 04:38:35 UTC  
> Url: https://github.com/boostorg/json/issues/994#issuecomment-2014341591  

I couldn't reproduce it. Can you please change this snippet (https://godbolt.org/z/4az5z7eva ), so that it hits that warning?

---

## Comment 2

> Username: infn-ke  
> Created at: 2024-03-27 08:47:59 UTC  
> Url: https://github.com/boostorg/json/issues/994#issuecomment-2022229096  

@grisumbras I can see issue in a larger code base. Will try to re-create in a smaller context.

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-10-09 13:56:49 UTC  
> Url: https://github.com/boostorg/json/issues/994#issuecomment-2402424488  

Since there's not follow-up, I'm closing this. If the issue still manifests, please reopen.
