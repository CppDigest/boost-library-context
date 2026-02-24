# #276 - Missing assignment operator=(std::initializer_list<value_type>) [Closed]

> Username: tlemo  
> Created at: 2024-09-01 03:51:56 UTC  
> Updated at: 2024-09-02 16:59:17 UTC  
> Closed at: 2024-09-02 16:56:14 UTC  
> Url: https://github.com/boostorg/unordered/issues/276  

The boost.unordered containers are missing the assignment operator taking an `std::initializer_list` argument. While it may seem redundant, this can be needed in a few special cases - in particular when using custom allocators which lack default initialization (as many stateful allocators are)

---

## Comment 1

> Username: joaquintides  
> Created at: 2024-09-02 16:59:16 UTC  
> Url: https://github.com/boostorg/unordered/issues/276#issuecomment-2325105433  

Fixed in https://github.com/boostorg/unordered/commit/cd9a592f0036f61f3314a7c3500dcc48228f7900, thanks for the report!
