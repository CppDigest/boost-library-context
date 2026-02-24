# #62 - Should leaf::result::value_type be public? [Closed]

> Username: Snarpix  
> Created at: 2023-09-27 13:22:00 UTC  
> Updated at: 2023-11-06 01:30:43 UTC  
> Closed at: 2023-11-06 01:30:43 UTC  
> Url: https://github.com/boostorg/leaf/issues/62  

Currently I have to use   
`std::remove_reference_t<decltype(std::declval<Result>().value())>`  
which is not ideal.

---

## Comment 1

> Username: zajo  
> Created at: 2023-10-29 05:40:45 UTC  
> Updated at: 2023-10-29 18:39:46 UTC  
> Url: https://github.com/boostorg/leaf/issues/62#issuecomment-1784005356  

`result<int &>::value_type` is `int`. Would this be correct in your use case, or should it be `int &`?

---

## Comment 2

> Username: Snarpix  
> Created at: 2023-10-31 08:11:50 UTC  
> Url: https://github.com/boostorg/leaf/issues/62#issuecomment-1786708920  

It should be int&, exactly as type parameter of result template.  
In my workaround it will be handled incorrectly, because I don't care about cv/ref.
