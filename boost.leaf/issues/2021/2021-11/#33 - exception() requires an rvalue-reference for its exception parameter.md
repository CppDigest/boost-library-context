# #33 - exception() requires an rvalue-reference for its exception parameter. [Closed]

> Username: vector-of-bool  
> Created at: 2021-11-21 05:13:34 UTC  
> Updated at: 2021-11-25 01:07:27 UTC  
> Closed at: 2021-11-25 01:07:26 UTC  
> Url: https://github.com/boostorg/leaf/issues/33  

The exception-aware overload of `exception()` look as follows (borrowing constraint syntax for conciseness):  
  
```c++  
template <typename Ex>  
  requires std::is_base_of<std::exception, Ex>  
exception(Ex&& ex) {  
  // Throws a class derived from 'Ex' ...  
}  
```  
  
This causes unexpected behavior if the exception object is an lvalue:  
  
```c++  
std::runtime_error exc{"Ouch"};  
throw leaf::exception(exc);  
```  
  
Because `is_base_of<std::exception, std::runtime_error&>` is `false`, the other overload of `exception()` is selected, and a bare `std::exception` object will be returned and then thrown, instead of throwing the `std::runtime_error exc`. The `runtime_error` will instead be loaded as an exception value, which can _sometimes_ be handled:  
  
```c++  
leaf::try_handle_some(  
  [] {  
    std::runtime_error exc{"Ouch"};  
    throw leaf::exception(exc);  
  },  
  [] (leaf::catch_<std::runtime_error>) {}, // [1]  
  [] (const std::runtime_error&) {}); // [2]  
```  
  
The `[1]` handler will not be invoked, but the `[2]` _will_.  
  
Removing cvref qualifiers in the check causes `exception` to behave "as expected:"  
  
```c++  
template <typename Ex>  
  requires std::is_base_of<std::exception, std::remove_cvref_t<Ex>>  
exception(Ex&& ex) {  
  // Throws a class derived from 'std::remove_cvref_t<Ex>' ...  
}  
```  
  
I'm thinking a simple fix is possible, but want to confirm that this is actually an unintended behavior, and whether there might be other code locations that could also be affected.

---

## Comment 1

> Username: zajo  
> Created at: 2021-11-21 07:22:34 UTC  
> Url: https://github.com/boostorg/leaf/issues/33#issuecomment-974768039  

Thank you. There is another bug, in that I have a static_assert if you attempt to pass an object of a type that derives from std::exception as anything but the first argument to leaf::exception, which also doesn't trigger for the same reason.

---

## Comment 2

> Username: zajo  
> Created at: 2021-11-25 01:07:26 UTC  
> Url: https://github.com/boostorg/leaf/issues/33#issuecomment-978685526  

FYI the fix for this and catching abstract exception types will ship with the upcoming Boost release.
