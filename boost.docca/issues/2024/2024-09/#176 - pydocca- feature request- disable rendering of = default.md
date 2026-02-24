# #176 - pydocca: feature request: disable rendering of = default [Closed]

> Username: anarthal  
> Created at: 2024-09-03 16:35:17 UTC  
> Updated at: 2024-10-03 11:17:26 UTC  
> Closed at: 2024-10-03 11:17:26 UTC  
> Url: https://github.com/boostorg/docca/issues/176  

I find rendering ` = default` in constructors and assignments misleading. I think it suggests that the operation is trivial, where this is usually not the case - the operation happens to not have a body and rely on private members' semantics, but does something (and that's why it's documented). Being defaulted is just an implementation detail. For instance:  
  
```cpp  
/// Closes a handle on exit  
class win_handle {  
    std::unique_ptr<HANDLE, detail::closer> impl_;  
public:  
    /// Move constructor, leaves other empty.  
    win_handle(win_handle&&) = default;  
};  
```  
  
I don't think rendering the `= default` for classes like this adds anything to the user, as it's not part of the API. It might be a matter of taste though.
