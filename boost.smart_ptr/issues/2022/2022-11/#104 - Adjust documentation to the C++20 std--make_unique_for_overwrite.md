# #104 - Adjust documentation to the C++20 std::make_unique_for_overwrite [Closed]

> Username: Ukilele  
> Created at: 2022-11-05 21:09:25 UTC  
> Updated at: 2022-12-15 13:36:02 UTC  
> Closed at: 2022-12-15 13:36:01 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/104  

Hey, I just wanted to issue a small nitpick.  
  
[make_unique.doc](https://github.com/boostorg/smart_ptr/blob/develop/doc/smart_ptr/make_unique.adoc) mentions the following sentences:  
  
> This library also provides additional overloads of make_unique for default-initialization, when users do not need or want to incur the expense of value-initialization. The C++ standard does not yet provide this feature with std::make_unique.  
  
The last sentence isn't up to date anymore as C++20 added [std::make_unique_for_overwrite](https://en.cppreference.com/w/cpp/memory/unique_ptr/make_unique).

---

## Comment 1

> Username: glenfe  
> Created at: 2022-12-15 13:36:01 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/104#issuecomment-1353076833  

Done, in 366c60a
