# #158 - Detecting boost_test_print_type does not work when testing a type with an explicit conversion to bool [Closed]

> Username: Marc-Aldorasi-Imprivata  
> Created at: 2018-09-11 18:20:33 UTC  
> Updated at: 2018-09-25 16:18:25 UTC  
> Closed at: 2018-09-25 16:18:25 UTC  
> Url: https://github.com/boostorg/test/issues/158  

```cpp  
struct s {  
	explicit operator bool() const { return false; }  
};  
std::ostream &boost_test_print_type(std::ostream &o, const s &) { return o << 's'; }  
```  
  
When testing a value of type `s` (e.g. `BOOST_TEST(s{})`), compilation fails because the compiler is looking for an overload of `operator<<`, but we haven't provided one.  As per the documentation, providing a definition of `boost_test_print_type` should be sufficient.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-09-25 16:18:25 UTC  
> Url: https://github.com/boostorg/test/issues/158#issuecomment-424408864  

See #159
