# #190 - Getting members at index >=4 works with const types but not non-const types [Closed]

> Username: devyte  
> Created at: 2024-10-25 21:40:33 UTC  
> Updated at: 2024-10-28 21:37:16 UTC  
> Closed at: 2024-10-28 21:37:15 UTC  
> Url: https://github.com/boostorg/pfr/issues/190  

This is a weird one, I'm not sure what to make of it.  
Platform:  
- Windows 11 Windows SDK version 10.0  
- Visual Studio Community 2022 v143  
- MSVC (output of cl.exe): Microsoft (R) C/C++ Optimizing Compiler Version 19.41.34123 for x64  
- C++ standard: C++20  
  
MCVE:  
```C++  
#include <boost/pfr.hpp>  // For reflection (no macros needed)  
#include <string>  
#include <vector>  
  
//Struct with max field index 3, getting last field works fine   
struct MyInnerStruct  
{  
	int aa;  
	std::string bb;  
	double cc;  
	std::vector<int> dd;  
};  
  
//Struct with max field index 4, getting last field doesn't works for const but not for non-const  
struct MyStruct  
{  
	int a;  
	std::string b;  
	double c;  
	bool d;  
	std::vector<int> e;  
};  
  
int main()  
{  
	MyInnerStruct myInnerStruct{ 100, "101", 102.0, {103, 104, 105} };  
	auto& fieldVectorInner = boost::pfr::get<3>(myInnerStruct); // <-- this works  
  
	const MyInnerStruct myInnerStruct_const{ 100, "101", 102.0, {103, 104, 105} };  
	auto& fieldVectorInner_const = boost::pfr::get<3>(myInnerStruct_const); // <-- this works  
  
	MyStruct myStruct{ 3, "4", 5.0, true, {6, 7, 8}};  
	auto& fieldVector = boost::pfr::get<4>(myStruct);    // <--- this seems to return a reference to an invalid address  
  
	const MyStruct myStruct_const{ 3, "4", 5.0, true,{6, 7, 8}};  
	auto& fieldVector_const = boost::pfr::get<4>(myStruct_const); // <-- this works  
}  
```  
  
I tried to follow with the debugger into the pfr headers, and I reached  core17.hpp where I found this:  
```C++  
template <class T>  
constexpr auto tie_as_tuple(T& val) noexcept {  
  static_assert(  
    !std::is_union<T>::value,  
    "====================> Boost.PFR: For safety reasons it is forbidden to reflect unions. See `Reflection of unions` section in the docs for more info."  
  );  
  typedef size_t_<boost::pfr::detail::fields_count<T>()> fields_count_tag;  
  return boost::pfr::detail::tie_as_tuple(val, fields_count_tag{});  
}  
```  
For the case of non-const MyStruct, the fields_count_tag seems to have a wrong value of 4. I tried to go deeper into fields_count, but I don't understand how its constexpr std::size_t result is calculated, I got stuck at the following, which I don't understand:  
```C++  
    constexpr std::size_t max_fields_count = (sizeof(type) * CHAR_BIT); // We multiply by CHAR_BIT because the type may have bitfields in T  
    constexpr std::size_t result = detail::detect_fields_count_dispatch<type>(size_t_<max_fields_count>{}, 1L, 1L);  
```

---

## Comment 1

> Username: devyte  
> Created at: 2024-10-28 21:37:15 UTC  
> Url: https://github.com/boostorg/pfr/issues/190#issuecomment-2442679955  

After poking at this for several days I found a workaround: I wrapped the call to boost::pfr::get<> in a template function and then specialized it for the non-const case:  
```C++  
	// Get a class's member reference by index  
	template <size_t Idx, typename T>  
	constexpr auto& Member(const T& classObj)  
	{  
		return boost::pfr::get<Idx>(classObj);  
	}  
  
	// Get a class's member reference by index  
	// Workaround for issue in PFR issue https://github.com/boostorg/pfr/issues/190  
	// Assumes that the field isn't const in a non-const aggregate.  
	template <size_t Idx, typename T>  
	constexpr auto& Member(T& classObj)  
	{  
		auto const& classObjConst = classObj;  
		auto& fieldConst = boost::pfr::get<Idx>(classObjConst);  
		auto& field = const_cast<std::decay_t<decltype(fieldConst)>&>(fieldConst);  
		return field;  
	}  
```  
Although very dirty, this worked as expected. However, I then encountered a different crash elsewhere in my code in the move constructor of std::vector, which made zero sense, so I started to doubt the compiler, my existence, and the universe as a whole.  
In summary, I had installed the boost v1.86 libs via nuget package, so I decided to go back to basics and try my same code built in Linux with clang++ and linked against a clean build of the boost libs v1.86 built from source, with clang++, using C++20.  
No more issues.  
  
So I'm guessing what I observed was either an issue with how the boost libs got built in the nuget package (https://github.com/sergey-shandar/getboost), or an issue with MSVC, or a combination of both.  
  
Closing.
