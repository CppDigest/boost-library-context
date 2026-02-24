# #159 - boost_test_print_type is not considered when printing out values with non-explicit operator bools [Closed]

> Username: Marc-Aldorasi-Imprivata  
> Created at: 2018-09-11 18:26:05 UTC  
> Updated at: 2018-09-25 16:18:06 UTC  
> Closed at: 2018-09-25 16:18:06 UTC  
> Url: https://github.com/boostorg/test/issues/159  

```cpp  
struct s  
{  
	operator bool() const { return false; }  
};  
std::ostream &boost_test_print_type(std::ostream &o, const s &) { return o << "s"; }  
  
struct s2  
{  
	operator bool() const { return false; }  
};  
std::ostream &operator<<(std::ostream &o, const s2 &) { return o << "s2"; }  
  
BOOST_AUTO_TEST_CASE(f)  
{  
	BOOST_TEST(s{});  
	BOOST_TEST(s2{});  
}  
```  
  
When printing out the failures in this test, the second check correctly uses `operator<<` to print out the value of `s2`, but the first check displays the value of `s` as 0.  `s` should be printed out using `boost_test_print_type` instead.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-09-12 21:45:03 UTC  
> Url: https://github.com/boostorg/test/issues/159#issuecomment-420808499  

Would you please check the branch `topic/GH-158-boost_test_print_type-printing-on-single-value`? It should solve this issue and the other you mentioned #158 .

---

## Comment 2

> Username: Marc-Aldorasi-Imprivata  
> Created at: 2018-09-13 13:49:36 UTC  
> Url: https://github.com/boostorg/test/issues/159#issuecomment-421013807  

That fixes both issues, thanks.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2018-09-25 16:18:06 UTC  
> Url: https://github.com/boostorg/test/issues/159#issuecomment-424408773  

Merged to develop, closing.
