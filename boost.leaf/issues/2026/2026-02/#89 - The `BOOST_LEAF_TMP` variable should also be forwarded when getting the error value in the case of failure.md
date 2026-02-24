# #89 - The `BOOST_LEAF_TMP` variable should also be forwarded when getting the error value in the case of failure [Closed]

> Username: aandrejevas  
> Created at: 2026-02-17 10:39:22 UTC  
> Updated at: 2026-02-18 20:29:19 UTC  
> Closed at: 2026-02-18 20:29:19 UTC  
> Url: https://github.com/boostorg/leaf/issues/89  

For example right now this is how the `BOOST_LEAF_CHECK` is defined:  
```cpp  
#   define BOOST_LEAF_CHECK(r)\  
        {\  
            auto && BOOST_LEAF_TMP = (r);\  
            static_assert(::boost::leaf::is_result_type<typename std::decay<decltype(BOOST_LEAF_TMP)>::type>::value,\  
                "BOOST_LEAF_CHECK requires a result object (see is_result_type)");\  
            if( !BOOST_LEAF_TMP )\  
                return BOOST_LEAF_TMP.error();\  
        }  
```  
But the return statement should be written like this:  
```cpp  
                return std::forward<decltype(BOOST_LEAF_TMP)>(BOOST_LEAF_TMP).error();  
```  
This is more efficient code in the cases where the result type has an `error()` member function with an rvalue ref-qualifier. For example `std::expected` has such a member function:  
  
<img width="504" height="168" alt="Image" src="https://github.com/user-attachments/assets/016534e6-1458-4cb2-aedf-28f37009f06a" />

---

## Comment 1

> Username: zajo  
> Created at: 2026-02-18 20:29:19 UTC  
> Url: https://github.com/boostorg/leaf/issues/89#issuecomment-3923051784  

Thnks. I have fixed this on `develop`. However, note that the only way you can use an external result type is if `.error()` returns an object of type `std::error_code`, or of a type that is implicitly convertible to `std::error_code`, which limits the usefulness of moving that object. Nevertheless, `std::forward` is correct here, I had missed that.
