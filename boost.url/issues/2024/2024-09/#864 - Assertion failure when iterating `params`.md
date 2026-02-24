# #864 - Assertion failure when iterating `params` [Closed]

> Username: martinus  
> Created at: 2024-09-20 07:34:57 UTC  
> Updated at: 2024-10-04 17:42:46 UTC  
> Closed at: 2024-10-04 14:32:09 UTC  
> Url: https://github.com/boostorg/url/issues/864  

While fuzzing a library that makes use of `boost::url` we found an assertation failure. It boils down to this reproducer:  
  
```cpp  
auto url = boost::urls::parse_relative_ref("?#");  
for (auto const& param : url->params()) {  
	// bang in 2nd param  
}  
```  
This gives me an assertation fault:  
```  
liburlutils-test: libs/url/src/detail/params_iter_impl.cpp:72: void boost::urls::detail::params_iter_impl::setup(): Assertion `pos != ref.size()' failed.  
```  
  
Same happens btw. with `auto url = boost::urls::parse_uri("http://foo.com?#");`

---

## Comment 1

> Username: martinus  
> Created at: 2024-09-20 11:04:32 UTC  
> Url: https://github.com/boostorg/url/issues/864#issuecomment-2363457940  

When building with asserts disabled the code runs through, but valgrind warns about `Conditional jump or move depends on uninitialised value`

---

## Comment 2

> Username: vinniefalco  
> Created at: 2024-09-24 22:09:25 UTC  
> Url: https://github.com/boostorg/url/issues/864#issuecomment-2372473940  

Given `"?#"` there is one param here: `{ "", null }`. That is, an empty key with a null value.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2024-09-24 22:41:49 UTC  
> Url: https://github.com/boostorg/url/issues/864#issuecomment-2372512667  

> Given "?#" there is one param here: { "", null }. That is, an empty key with a null value.  
  
The issue here goes beyond that. Boost.URL is interpreting "?#" as two parameters. This one is a parsing error. The error in the implementation is related to this design choice, but it's not only about it.

---

## Comment 4

> Username: martinus  
> Created at: 2024-10-04 17:42:45 UTC  
> Url: https://github.com/boostorg/url/issues/864#issuecomment-2394262458  

Thanks!
