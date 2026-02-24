# #63 - Bad Documentation or library source code [Closed]

> Username: heretic13  
> Created at: 2022-08-15 08:20:35 UTC  
> Updated at: 2022-08-15 14:57:23 UTC  
> Closed at: 2022-08-15 14:57:23 UTC  
> Url: https://github.com/boostorg/multi_index/issues/63  

Hello.  
  
I have a task:  
Create a template class that internally uses a multi_index_container with a data type that depends on the class template parameter.  
  
I wrote the implementation code for VS2022, but under Linux (gcc) it didn't want to compile.  
  
The compiler throws errors, but I don't see any errors in the code.  
  
Below is an example and my solution.  
  
In this regard, I have questions for the developers:  
1. Fix the multi_index library code so that it compiles under Linux and Windows.  
or  
2. Make changes to the documentation. I reviewed carefully. boost::get<> is not mentioned as a means of obtaining keys for a container, it is an undocumented feature.

---

## Comment 1

> Username: heretic13  
> Created at: 2022-08-15 08:21:28 UTC  
> Url: https://github.com/boostorg/multi_index/issues/63#issuecomment-1214745005  

[errors.log](https://github.com/boostorg/multi_index/files/9335976/errors.log)  
[TestMi.cpp.txt](https://github.com/boostorg/multi_index/files/9335979/TestMi.cpp.txt)

---

## Comment 2

> Username: joaquintides  
> Created at: 2022-08-15 14:18:41 UTC  
> Updated at: 2022-08-15 14:43:59 UTC  
> Url: https://github.com/boostorg/multi_index/issues/63#issuecomment-1215067218  

Hi,  
  
Your `fun2` incorrectly tries to invoke the template member function `EpsContainer::get` in a so-called dependent context without using the *required* `template` disambiguator. It should be written like this:  
  
```cpp  
void fun2()  
{  
	const auto& byIdIndex1 = m_EndPoints.template get<0>();  
	const auto& byIdIndex2 = m_EndPoints.template get<TagByEpId>();  
}  
```  
  
[Here](https://en.cppreference.com/w/cpp/language/dependent_name#The_template_disambiguator_for_dependent_names) you can find an explanation on why the `template` keyword is needed in this particular context. As it happens, MSVC accepts the code without `template`, but this is not in accordance with the C++ standard. You are not the first one to be bitten by this admittedly cumbersome rule of C++.  
  
As for global `boost::get` (as applied to `multi_index_container`), it is documented. Look for "multi_index_container global functions for index retrieval" in [class template `multi_index_container` reference](https://www.boost.org//libs/multi_index/doc/reference/multi_index_container.html#multi_index_container). Global `get` is defined in namespace `boost::multi_index` but lifted to namespace `boost` by means of a using-declaration, as documented in [header `"boost/multi_index_container.hpp"` synopsis](https://www.boost.org/libs/multi_index/doc/reference/multi_index_container.html#synopsis) (look for `using multi_index::get` there).

---

## Comment 3

> Username: heretic13  
> Created at: 2022-08-15 14:54:32 UTC  
> Url: https://github.com/boostorg/multi_index/issues/63#issuecomment-1215108538  

Excellent.  
Thanks for the clarification.  
  
The topic can be closed.
