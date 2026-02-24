# #60 - want to add `pointers to data members -> index` [Open]

> Username: Fuyutsubaki  
> Created at: 2020-11-19 19:30:33 UTC  
> Updated at: 2022-08-26 14:55:39 UTC  
> Url: https://github.com/boostorg/pfr/issues/60  

```C++  
template<class T>  
struct DelayConstruct {  
	static inline T value{};  // construct in runtime.    
};  
template<class T, class M>  
constexpr std::size_t get_index(M T::*mem_ptr) {  
	auto &t = DelayConstruct<T>::value;  
	return  
		std::apply([&](auto&...e) {  
			int idx = 0;  
			std::array<void*, sizeof...(e)> list = { (void*)&e ... };  
			for (auto b : list) {  
				if (b == &(t.*mem_ptr))  
					return idx;  
				idx += 1;  
			}  
			return 42;  
		}, structure_tie(t));  
}  
```  
  
```C++  
//example1  
struct S {  
	std::string x;  
	std::vector<int> y;  
	std::string z;  
};  
  
static_assert(boost::pfr::get_index(&S::x) == 0);  
static_assert(boost::pfr::get_index(&S::y) == 1);  
static_assert(boost::pfr::get_index(&S::z) == 2);  
```  
  
```C++  
//example2  
struct S {  
	static constexpr auto names = std::make_tuple("x","y","z");  
	std::string x;  
	std::vector<int> y;  
	std::string z;  
};  
  
assert(std::string_view{"x"} == std::get<boost::pfr::get_index(&S::x)>(S::names));  
assert(std::string_view{"y"} == std::get<boost::pfr::get_index(&S::y)>(S::names));  
assert(std::string_view{"z"} == std::get<boost::pfr::get_index(&S::z)>(S::names));  
```

---

## Comment 1

> Username: asfernandes  
> Created at: 2022-04-27 19:30:14 UTC  
> Url: https://github.com/boostorg/pfr/issues/60#issuecomment-1111399314  

Could this be added to the library?

---

## Comment 2

> Username: BenFrantzDale  
> Created at: 2022-08-26 14:55:38 UTC  
> Url: https://github.com/boostorg/pfr/issues/60#issuecomment-1228598374  

I landed here looking for the reverse (Related: https://stackoverflow.com/questions/72889401/can-boost-pfr-be-used-to-iterate-the-fields-of-a-type-as-member-pointers) I want   
```  
template <typename T, std::size_t Index>  
using ith_member_type = decltype(boost::pfr::get<Index>(std::declval<T>()));  
  
template <typename T, std::size_t Index>  
auto get_member_pointer() -> ith_member_type<T, Index> T::* {  
    ???  
}  
```
