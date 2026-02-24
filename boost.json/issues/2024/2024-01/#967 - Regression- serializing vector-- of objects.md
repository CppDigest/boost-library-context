# #967 - Regression: serializing vector<> of objects [Closed]

> Username: matwey  
> Created at: 2024-01-04 14:53:47 UTC  
> Updated at: 2024-10-09 14:06:49 UTC  
> Closed at: 2024-10-09 14:06:49 UTC  
> Url: https://github.com/boostorg/json/issues/967  

Hello,  
  
I've found that the behavior of the following code snipped is different for different boost versions.  
  
### Version of Boost  
  
* 1.75.0:  
```  
[[42],[42],[42],[42]]  
```  
  
* 1.79.0, 1.81.0, 1.83.0:  
```  
[42]  
```  
  
I believe that 1.75.0 behavior should be correct here.  
  
### Steps necessary to reproduce the problem  
  
```c++  
#include <iostream>  
#include <vector>  
  
#include <boost/json.hpp>  
  
  
class any_result {  
public:  
	inline any_result() {}  
	template<class T> any_result(const T&) {}  
	template<class T> any_result(T&&) {}  
  
	void value_from(boost::json::value& jv) const;  
};  
  
void any_result::value_from(boost::json::value& jv) const {  
	jv = { 42 };  
}  
  
inline void tag_invoke(boost::json::value_from_tag, boost::json::value& jv, const any_result& r) {  
	r.value_from(jv);  
}  
  
template<class T>  
auto save(T&& t) {  
	return boost::json::value_from(std::forward<T>(t));  
}  
  
template<class T>  
auto save_json(T&& t) {  
	return boost::json::serialize(save(std::forward<T>(t)));  
}  
  
int main(int argc, char** argv) {  
	std::vector<any_result> r;  
  
	r.reserve(4);  
	for (std::size_t i = 0; i < r.capacity(); ++i) {  
		r.emplace_back();  
	}  
  
	std::cout << save_json(r) << std::endl;  
  
	return 0;  
}  
```  
  
### All relevant compiler information  
  
gcc 9--13 have been tested on Wandbox.

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-01-04 16:36:28 UTC  
> Updated at: 2024-01-04 16:37:17 UTC  
> Url: https://github.com/boostorg/json/issues/967#issuecomment-1877410242  

There are multiple things going on here. First of all, your `any_result` has an overly tolerant constructor. As a result, `any_result x = std::vector<any_result>();` compiles. In addition, C++ considers the namespace of `any_result` to be associated for `std::vector<any_result>`. Together, these two things lead to   
```c++  
vector<any_result> v;  
boost::json::value jv;  
tag_invoke(boost::json::value_from_tag(), jv, v);  
```  
choosing your `tag_invoke` overload. Now, in older versions of Boost.JSON a library-provided implementation was always used for containers, but that lead to a problem where a user provides a more specific implementation, but the library disregards it. So, now we are ranking user-provided overloads higher and always prefer them over generic implementation.  
  
The second thing is that `jv = { x };` on some compilers called `value(std::initializer_list<value_ref>)` constructor, and on others `value(T)` constructor. This caused incosistency between compilers, which we removed by special treatment of initializer_lists of size 1. Now they are equivalent to `value(x)`.  
  
If you want the previous behaviour   
1. Make `any_result` constructors `explicit`.  
2. use `jv = {{ 42 }};` or `jv.emplace_array().emplace(42);`

---

## Comment 2

> Username: grisumbras  
> Created at: 2024-01-04 16:40:48 UTC  
> Updated at: 2024-01-04 17:07:27 UTC  
> Url: https://github.com/boostorg/json/issues/967#issuecomment-1877416875  

Correction: the "second thing" is only applicable to version 1.84.0, in which your test would produce `42` and not `[42]`.

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-01-04 17:07:15 UTC  
> Url: https://github.com/boostorg/json/issues/967#issuecomment-1877464977  

If making `any_result`'s constructor `explicit` is undesirable you can instead do this:  
  
```c++  
struct any_result_arg  
{  
    any_result const& r;  
  
    any_result_arg(any_result const& r) : r(r) {}  
};  
  
void  
tag_invoke(boost::json::value_from_tag, boost::json::value& jv, any_result_arg const& arg)  
{  
    arg.r.value_from(jv);  
}  
```  
  
`any_result_arg` has a more restrictive constructor which solves issue number 1.

---

## Comment 4

> Username: grisumbras  
> Created at: 2024-01-04 17:59:27 UTC  
> Url: https://github.com/boostorg/json/issues/967#issuecomment-1877532880  

Yet another version:  
  
```c++  
  
template< class T >  
typename std::enable_if< std::is_same<T, any_result>::value >::type  
tag_invoke(  
    boost::json::value_from_tag, boost::json::value& jv, const T& r)  
{  
    r.value_from(jv);  
}  
```

---

## Comment 5

> Username: grisumbras  
> Created at: 2024-10-09 14:06:49 UTC  
> Url: https://github.com/boostorg/json/issues/967#issuecomment-2402451482  

Since there were no complaints for any of my suggestions, I consider this solved.
