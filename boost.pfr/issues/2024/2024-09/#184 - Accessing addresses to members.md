# #184 - Accessing addresses to members [Open]

> Username: very-cool-name  
> Created at: 2024-09-16 11:21:18 UTC  
> Updated at: 2024-11-07 14:10:33 UTC  
> Url: https://github.com/boostorg/pfr/issues/184  

Hey, I have some pybinded structs, that have a lot of repeating boilerplate:  
  
```cpp  
struct MyStruct {  
  int x;  
  char c;  
};  
  
py::class_< MyStruct >(module, "MyStruct")  
  .def_readonly("x", &MyStruct::x)  
  .def_readonly("c", &MyStruct::c);  
```  
  
I was thinking about using `boost::pfr` to automate this, but I don't think there is currently any way, since `boost::pfr` doesn't have an interface to access member addresses i.e. something like this:  
  
```cpp  
auto cls = py::class_< MyStruct >(module, "MyStruct");  
boost::pfr::for_each_member_address_with_name<MyStruct>([cls](std::string_view name, auto address) {  
  cls.def_readonly(name, address);  
});  
```  
  
Is there any interest in having such interface? And if so - I think I can make a PR. If you could give me some hints on possible implementation - that would be greatly appreciated.  
  
Thanks!

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-10-17 09:08:08 UTC  
> Url: https://github.com/boostorg/pfr/issues/184#issuecomment-2418983097  

Looks quite interesting. Please provide a PR.

---

## Comment 2

> Username: very-cool-name  
> Created at: 2024-10-29 12:11:54 UTC  
> Url: https://github.com/boostorg/pfr/issues/184#issuecomment-2444039676  

Gave it some thought and I don't really know how to implement it. Structured bindings won't help here, since essentially:  
```  
struct S { char c; };  
  
S s;  
auto& [c] = s;  
// there is no way to get from ref c to char S::*  
```  
  
If you are aware of maybe compiler specific tricks that can let me do that - please help me.

---

## Comment 3

> Username: hansalemaos  
> Created at: 2024-11-07 09:46:56 UTC  
> Updated at: 2024-11-07 10:13:13 UTC  
> Url: https://github.com/boostorg/pfr/issues/184#issuecomment-2461771904  

Maybe something like this? Probably not the safest version, but it works :)  
  
```cpp   
  
template <typename Ts, typename Us>  
void change_data_without_a_check(Ts& mystruct, const std::string_view column_name, const Us new_data) {  
	size_t column_id = 0;  
	constexpr auto field_names = pfr::names_as_array<Ts>();  
	for (size_t i = 0; i < field_names.size(); i++) {  
		if (field_names[i].compare(column_name) == 0) {  
			column_id = i;  
		}  
	}  
	size_t i = 0;  
  
	pfr::for_each_field(mystruct, [&](auto& f, auto meta) {  
		if (i == column_id) {  
			void* voidptr = reinterpret_cast<void*>(&(pfr::get<meta>(mystruct))); // this part gives me the mem address of each field (msvc - c++20)  
			(*(Us*)(voidptr)) = new_data; // ... and some "don't try this at home" assignments  
		}  
		i++;  
  
		});  
  
}  
struct S { char c; uint8_t u; };  
int main(int argc, const char* argv[]) {  
  
	auto s2x = S('A');  
	uint8_t newvaluex = 100;  
	std::string mycolumnx = "c";  
	change_data_without_a_check(s2x, mycolumnx, newvaluex);  
	char newvaluex2 = 'A';  
	std::string mycolumnx2 = "u";  
	change_data_without_a_check(s2x, mycolumnx2, newvaluex2);  
	std::cout << s2x.c << " " << s2x.u << std::endl; } ```

---

## Comment 4

> Username: very-cool-name  
> Created at: 2024-11-07 13:09:17 UTC  
> Url: https://github.com/boostorg/pfr/issues/184#issuecomment-2462199895  

As far as I understand, in the code above there is no point at which we have pointer of type `Us Ts::*`.   
This `reinterpret_cast<void*>(&(pfr::get<meta>(mystruct)));` is indeed the memory address, but I don't know of any way to make a hop from memory address to a pointer-to-member.

---

## Comment 5

> Username: hansalemaos  
> Created at: 2024-11-07 14:10:32 UTC  
> Url: https://github.com/boostorg/pfr/issues/184#issuecomment-2462336275  

Got it, I thought your main concern was getting the memory address of each member.
