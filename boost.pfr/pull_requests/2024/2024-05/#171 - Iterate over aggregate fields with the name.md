# #171 Iterate over aggregate fields with the name [Merged]

> Username: Baduit  
> Created at: 2024-05-14 23:32:12 UTC  
> Updated at: 2024-09-13 09:16:56 UTC  
> Merged at: 2024-09-13 08:57:49 UTC  
> Closed at: 2024-09-13 08:57:49 UTC  
> Url: https://github.com/boostorg/pfr/pull/171  

Add a new function `for_each_field_with_name`  
  
The goal is to be able to do this:  
```C++  
struct Toto  
{  
	int a;  
	char c;  
};  
  
Toto t {5, 'c'};  
auto cb = [](std::string_view name, const auto& value){ std::cout << "Name: " << name << " Value: " << value << std::endl; };  
boost::pfr::for_each_field_with_name(t, cb);  
```  
  
I think this is more user friendly than we way it can be done without this PR:  
```C++  
auto cb2 = []<std::size_t Index>(const auto& value, std::integral_constant<std::size_t, Index> i)  
{  
	std::cout << "Name: " << boost::pfr::get_name<Index, Toto>() << " Value: " << value << std::endl;  
};  
boost::pfr::for_each_field(t, cb2);  
```  
  
I tried to make the code of `for_each_field_with_name` as close as possible to `for_each_field`.  
  
I will add new tests if you think this feature could be merged in the future.  
  
I have explored a bit the idea of completing `for_each_field` instead of creating a new function in this branch https://github.com/Baduit/pfr/tree/feature/for_each_field_can_provide_name, most of the work is done and it makes possible to do stuff like this:  
```C++  
void plop () {  
	std::map<std::string, std::string> m;  
	auto fill = [&m](std::string_view name, const auto& value){  
		m[std::string(name)] = value;  
	};  
  
	boost::pfr::for_each_field(SimpleStruct{ 'e', "test"}, fill);  
  
	assert(m.size() == 2);  
	assert(m["c"] == "e");  
	assert(m["str"] == "test");  
}  
  
void plop2 () {  
	std::map<std::string, std::string> m;  
	std::map<std::string, std::size_t> mi;  
	auto fill = [&m, &mi](std::string_view name, const auto& value, std::size_t i){  
		m[std::string(name)] = value;  
		mi[std::string(name)] = i;  
	};  
  
	boost::pfr::for_each_field(SimpleStruct{ 'e', "test"}, fill);  
	assert(m.size() == 2);  
	assert(m["c"] == "e");  
	assert(m["str"] == "test");  
	assert(mi.size() == 2);  
	assert(mi["c"] == 0);  
	assert(mi["str"] == 1);  
}  
```  
Do you think it is a better idea ?

---

## Review 1 [Changes requested]

> Username: apolukhin  
> Created_at: 2024-06-21 06:40:59 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/pfr/pull/171#pullrequestreview-2131874229  

Looks good!  
  
Please see the comments to make the header changes smaller. Also some docs updates are required

📁 include/boost/pfr/core_name.hpp

```diff
 102 |+ template <class T, class F>
 103 |+ constexpr void for_each_field_with_name(T&& value, F&& func) {
 104 |+     return detail::for_each_field_with_name(std::forward<T>(value), std::forward<F>(func));
```

> Username: apolukhin  
> Created_at: 2024-06-21 06:35:45 UTC  
> Updated_at: 2024-06-21 06:42:31 UTC  
> Url: https://github.com/boostorg/pfr/pull/171#discussion_r1648482073  

This could be implemented in a simpler way:  
```  
return boost::pfr::for_each_field(  
    std::forward<T>(value),  
    [f = std::forward<F>(func)](auto&& t, auto index) mutable {  
        constexpr auto name = boost::pfr::get_name<decltype(index)::value>();  
        if constexpr (std::is_invokable_v<F, ?????? >) {  
          f(name, std::forward<decltype(t)>(t), index);  
        } else {  
          f(name, std::forward<decltype(t)>(t));  
        }  
    });  
```


📁 include/boost/pfr/detail/for_each_field_with_name_impl.hpp

```diff
  62 |+ }}} // namespace boost::pfr::detail
  63 |+ 
  64 |+ #endif // BOOST_PFR_DETAIL_FOR_EACH_FIELD_WITH_NAME_IMPL_HPP
```

> Username: apolukhin  
> Created_at: 2024-06-21 06:39:25 UTC  
> Updated_at: 2024-06-21 06:40:59 UTC  
> Url: https://github.com/boostorg/pfr/pull/171#discussion_r1648485045  

Please update the documentation file https://github.com/boostorg/pfr/blob/develop/doc/pfr.qbk . Add after pfr_quick_examples_for_each another table row with pfr_quick_examples_for_each_with_name and add pfr_quick_examples_for_each_with_name example to https://github.com/boostorg/pfr/tree/develop/example


---

## Comment 2

> Username: Baduit  
> Created_at: 2024-06-25 10:54:02 UTC  
> Url: https://github.com/boostorg/pfr/pull/171#issuecomment-2188602430  

> Looks good!  
>   
> Please see the comments to make the header changes smaller. Also some docs updates are required  
  
I updated my PR according to your comments.  
  
But on the github action I see a failure that I don't understand https://github.com/Baduit/pfr/actions/runs/9660279058/job/26645491447

---

## Comment 3

> Username: Baduit  
> Created_at: 2024-08-27 21:48:30 UTC  
> Url: https://github.com/boostorg/pfr/pull/171#issuecomment-2313638419  

I updated my branch and thanks to your commit to fix the CI the builds are green

---

## Comment 4

> Username: coveralls  
> Created_at: 2024-09-13 08:50:13 UTC  
> Url: https://github.com/boostorg/pfr/pull/171#issuecomment-2348397906  

## Pull Request Test Coverage Report for [Build 10586042298](https://coveralls.io/builds/69780103)  
  
  
### Details  
  
* **0** of **0**   changed or added relevant lines in **0** files are covered.  
* No unchanged relevant lines lost coverage.  
* Overall coverage remained the same at **100.0%**  
  
---  
  
  
  
|  Totals | [![Coverage Status](https://coveralls.io/builds/69780103/badge)](https://coveralls.io/builds/69780103) |  
| :-- | --: |  
| Change from base [Build 10537504895](https://coveralls.io/builds/69407932): |  0.0% |  
| Covered Lines: | 405 |  
| Relevant Lines: | 405 |  
  
---  
##### 💛  - [Coveralls](https://coveralls.io)

---

## Comment 5

> Username: apolukhin  
> Created_at: 2024-09-13 09:16:55 UTC  
> Url: https://github.com/boostorg/pfr/pull/171#issuecomment-2348453878  

Many thanks for the PR!

---
