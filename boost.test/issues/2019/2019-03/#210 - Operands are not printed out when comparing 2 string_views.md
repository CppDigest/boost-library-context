# #210 - Operands are not printed out when comparing 2 string_views [Closed]

> Username: Marc-Aldorasi-Imprivata  
> Created at: 2019-03-13 20:15:34 UTC  
> Updated at: 2020-01-07 06:44:29 UTC  
> Closed at: 2019-11-03 10:13:52 UTC  
> Url: https://github.com/boostorg/test/issues/210  

```c++  
BOOST_AUTO_TEST_CASE(strs)  
{  
	std::string str = "str";  
	std::string_view sv = "sv";  
	std::experimental::string_view sve = "sve";  
  
	BOOST_TEST_CHECK(str != str);  
	BOOST_TEST_CHECK(sv != sv);  
	BOOST_TEST_CHECK(sve != sve);  
}  
```  
  
This test prints out  
  
```  
x.cpp(13): error: in "strs": check str != str has failed [str == str]  
x.cpp(14): error: in "strs": check sv != sv has failed  
x.cpp(15): error: in "strs": check sve != sve has failed  
```  
  
Comparing 2 string_views should print out their contents when the comparison fails, just like it does with string.  Comparing a string_view with a string or a C string does print out its contents; this issue only occurs when comparing a string_view with another string_view.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-03-13 20:17:21 UTC  
> Updated at: 2019-03-13 20:17:56 UTC  
> Url: https://github.com/boostorg/test/issues/210#issuecomment-472588272  

Thanks for the report. In your example, why `std::experimental::string_view` ? I mean I can understand adding support for `std::string_view`, but I am a bit reluctant for `std::experimental::string_view`

---

## Comment 2

> Username: Marc-Aldorasi-Imprivata  
> Created at: 2019-03-13 21:38:56 UTC  
> Url: https://github.com/boostorg/test/issues/210#issuecomment-472616283  

I was just seeing if it exhibited the same problem as `std::string_view`; I don't think supporting it is particularly important.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2019-10-23 17:20:16 UTC  
> Url: https://github.com/boostorg/test/issues/210#issuecomment-545548330  

I have an implementation on the branch `topic/GH-210-comparing-string-views`. Should be out for 1.72 but it would be nice if you can give it a try.

---

## Comment 4

> Username: Marc-Aldorasi-Imprivata  
> Created at: 2019-10-24 14:11:30 UTC  
> Url: https://github.com/boostorg/test/issues/210#issuecomment-545936750  

Yeah, that branch works for me.
