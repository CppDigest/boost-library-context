# #99 - Missing `[[nodiscard]]` attribute on `empty()` [Closed]

> Username: cmazakas  
> Created at: 2022-02-14 20:47:37 UTC  
> Updated at: 2022-02-25 22:37:34 UTC  
> Closed at: 2022-02-25 22:37:33 UTC  
> Url: https://github.com/boostorg/unordered/issues/99  

In the standard, `empty()` is defined as:  
```cpp  
    // capacity  
    [[nodiscard]] bool empty() const noexcept;  
    size_type size() const noexcept;  
    size_type max_size() const noexcept;  
```  
  
This simply just requires adding `BOOST_ATTRIBUTE_NODISCARD` to the member functions.

---

## Comment 1

> Username: cmazakas  
> Created at: 2022-02-25 22:37:33 UTC  
> Url: https://github.com/boostorg/unordered/issues/99#issuecomment-1051328012  

Fixed by #106
