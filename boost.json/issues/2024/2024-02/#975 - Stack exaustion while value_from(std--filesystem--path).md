# #975 - Stack exaustion while value_from(std::filesystem::path) [Closed]

> Username: RoyBellingan  
> Created at: 2024-02-01 02:16:56 UTC  
> Updated at: 2024-02-07 14:46:53 UTC  
> Closed at: 2024-02-07 14:46:53 UTC  
> Url: https://github.com/boostorg/json/issues/975  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
Are you inviting me to have multiple account or more friend ?  
  
### Version of Boost  
  
1.84  
  
### Steps necessary to reproduce the problem  
  
```Cpp  
#include <boost/json/src.hpp>  
#include <filesystem>  
#include <iostream>  
  
namespace bj = boost::json;  
  
int main() {  
	std::filesystem::path p = "/cry";  
	  
	auto ref = bj::value_from(p);  
	auto ser = bj::serialize(ref);  
	std::cout << ser;  
}  
```  
  
if the path is EMPTY it works fine, but if there is something it leads to a stack exausting crash, remaining struct inside those 2  
  
loop (first one is gcc the other clang, quite similar but a bit different)  
  
  
![image(8)](https://github.com/boostorg/json/assets/368572/6873051a-e0de-4712-95ba-509e57e35237)  
  
![image(9)](https://github.com/boostorg/json/assets/368572/66819e6c-8adb-44af-b9ba-0e924476edf1)  
  
Also using  
  
```Cpp  
void tag_invoke(const bj::value_from_tag&, bj::value& jv, const std::filesystem::path& t) {  
	jv = t.string();  
}  
  
std::filesystem::path tag_invoke(const bj::value_to_tag<std::filesystem::path>&, const boost::json::value& jv) {  
	std::filesystem::path path = std::string_view(jv.as_string());  
	return path;  
}  
  
or  
  
void tag_invoke(const bj::value_from_tag&, bj::value& jv, const std::filesystem::__cxx11::path& t) {  
	jv = t.string();  
}  
  
std::filesystem::path tag_invoke(const bj::value_to_tag<std::filesystem::__cxx11::path>&, const boost::json::value& jv) {  
	std::filesystem::path path = std::string_view(jv.as_string());  
	return path;  
}  
```  
  
Does not help (in fact looks like they are not even used...)

---

## Comment 1

> Username: RoyBellingan  
> Created at: 2024-02-01 02:56:42 UTC  
> Url: https://github.com/boostorg/json/issues/975#issuecomment-1920406216  

The solution is to put the tag_invoke INSIDE the boost::json namespace...  
  
This already happened in another case, for std::chrono,  
  
I think it should be worth to add a note into this page  
  
https://www.boost.org/doc/libs/1_84_0/libs/json/doc/html/json/conversion/custom_conversions.html  
  
To express that, sometimes... you have to put in namespace to help it!

---

## Comment 2

> Username: grisumbras  
> Created at: 2024-02-01 08:30:37 UTC  
> Url: https://github.com/boostorg/json/issues/975#issuecomment-1920765113  

The solution (to the error) is to not treat `path` as a sequence. It's not what users want anyway (everyone would prefer conversion to a string). This will result in `value_to<path>` being a compilation error.  
  
The next step will be to add a conversion category for paths.

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-02-01 08:32:47 UTC  
> Updated at: 2024-02-01 08:33:20 UTC  
> Url: https://github.com/boostorg/json/issues/975#issuecomment-1920768270  

> The solution is to put the tag_invoke INSIDE the boost::json namespace...  
> I think it should be worth to add a note into this page  
  
From that page:  
> embed the user-defined type into the arguments list (e.g. by using a tag type template such as value_to_tag<T>) so that its [associated namespaces and entities](http://eel.is/c++draft/basic.lookup.argdep#2) are examined when name lookup is performed.  
  
Maybe we should be less cryptic.

---

## Comment 4

> Username: RoyBellingan  
> Created at: 2024-02-01 14:46:35 UTC  
> Url: https://github.com/boostorg/json/issues/975#issuecomment-1921502884  

Yes maybe just drop a few lines about that specific example ?  
Is ok if I do a pull request for https://github.com/boostorg/json/blob/db92f8c22360990f450fe27b86ea1a5830b5cf05/doc/qbk/conversion/custom.qbk#L54 ?

---

## Comment 5

> Username: grisumbras  
> Created at: 2024-02-02 08:18:51 UTC  
> Url: https://github.com/boostorg/json/issues/975#issuecomment-1923301291  

You can create a PR, but before please look for a good place to put the note.
