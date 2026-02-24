# #897 - Delete rvalues if lvalues required [Closed]

> Username: Spixmaster  
> Created at: 2025-01-30 15:07:40 UTC  
> Updated at: 2025-02-01 15:55:43 UTC  
> Closed at: 2025-02-01 15:55:43 UTC  
> Url: https://github.com/boostorg/url/issues/897  

Take a look at the following example.  
  
```cpp  
//Compiles but produces garbage at runtime.  
const boost::urls::url_view url_1 = boost::urls::parse_uri(setting.as<std::string>()).value();  
  
//Compiles and works at runtime.  
const std::string tmp = setting.as<std::string>();  
const boost::urls::url_view url_2 = boost::urls::parse_uri(tmp).value();  
```  
  
The best solution would be to prohibit passing rvalues, `system::result<url_view> parse_uri(core::string_view &&s) = delete;`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2025-01-30 17:12:20 UTC  
> Url: https://github.com/boostorg/url/issues/897#issuecomment-2625090375  

Isn't this a general problem with `string_view`?

---

## Comment 2

> Username: Spixmaster  
> Created at: 2025-02-01 10:01:20 UTC  
> Url: https://github.com/boostorg/url/issues/897#issuecomment-2628885309  

Personally, I do not use `std::string_view`. So I do not have expertise with the matter.  
  
Maybe you could apply a global solution that the assignment of rvalues to `core::string_view` is prohibited. I am not sure whether that would solve the issue with rvalues as arguments.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2025-02-01 13:03:17 UTC  
> Url: https://github.com/boostorg/url/issues/897#issuecomment-2628945703  

That is a general issue with all view types (see all view types in C++). Passing an rvalue (temporary) to a view type can lead to a dangling reference. Still, they usually accept rvalues anyway and make it the user's responsibility to identify if the situation is one where the object will be immediately used. The rationale for accepting rvalues is because there are cases where this is useful: 1) when the element is immediately needed in the function where we want a common interface before the object goes out of scope, 2) uniformity with lvalues, and 3) making them generic for any sequence of elements.  
  
For instance,  
  
```cpp  
void foo(std::string_view view) {  
    std::cout << view << std::endl;  
}  
  
// ...  
// This would break if rvalues weren't allowed  
foo(type_convertible_to_string_view("bar"));  
  
// We would always need this instead  
type_convertible_to_string_view v("bar");  
foo(v);  
```  
  
We can't change string_view. We could forbid rvalues directly in Boost.URL but 1) that has technical implementation problems (Boost.URL accepts string_view as inputs, and the rvalues will still be implicitly convertible to string_view) and 2) that wouldn't change the tradeoffs very much because url_view is still a view type and the user is allowed to use it in a context where it's going to be immediately used.  
  
```cpp  
void foo(boost::urls::url_view const& view) {  
    // Immediately use the view  
}  
  
// ...  
// This would break if rvalues weren't allowed  
foo("https://bar.com");  
  
// We would always need this instead  
type_convertible_to_url_view v("https://bar.com");  
foo(v);  
```  
  
It would break the convention with all other view types and break existing code depending on this behavior. That's why it's the user's responsibility to ensure the object's lifetime is longer than the lifetime of the view.

---

## Comment 4

> Username: Spixmaster  
> Created at: 2025-02-01 14:34:30 UTC  
> Url: https://github.com/boostorg/url/issues/897#issuecomment-2628976602  

Alright, thank you for your thouhts on it. It is your choice to close this issue.
