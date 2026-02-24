# #79 - boost::algorithm::split_regex doesn't work with boost::u32regex. [Open]

> Username: kilasuelika  
> Created at: 2020-10-06 02:20:07 UTC  
> Updated at: 2020-10-06 02:20:43 UTC  
> Url: https://github.com/boostorg/algorithm/issues/79  

I want to split a UTF-8 string into tokens. However it looks like that **split_regex()** failed and just return original string. I also tried `u32regex_replace()` and it indeed replaced successfully.  
  
src.cpp:  
```  
// CutWord.cpp : This file contains the 'main' function. Program execution begins and ends there.  
//  
  
#include <iostream>  
#include<vector>  
#include<string>  
  
#include <boost/algorithm/string.hpp>  
#include<boost/algorithm/string/regex.hpp>  
#include <boost/regex/icu.hpp>   
#include<regex>  
  
using namespace std;  
  
int main()  
{  
	string str="数学。数学。数学。";  
	  
	//Split  
	vector<string> vs;  
        //regexU3 stores expression.  
	boost::u32regex regexU32= boost::make_u32regex("。");  
	boost::split_regex(vs, str,regexU32);  
	cout<<vs.size()<<endl; //only return 1.  
	  
	//Check if u32regex_replace works.  
	string nsub=".";  
	string nstr=u32regex_replace(str, regexU32, nsub);  
	cout<<nstr<<endl;  
};  
  
```  
  
CMakeLists.txt:  
```  
cmake_minimum_required(VERSION 3.10)  
project(main)  
set(CMAKE_C_COMPILER /usr/bin/gcc CACHE PATH "" FORCE)  
set(CMAKE_CXX_COMPILER /usr/bin/g++ CACHE PATH "" FORCE)  
  
include_directories("/usr/local/include")  
include_directories("./include")  
  
add_executable(mp src.cpp)  
  
#ICU  
find_package(ICU COMPONENTS i18n  io dt in uc)  
target_link_libraries(mp ${ICU_LIBRARIES})  
  
#boost  
find_package( Boost COMPONENTS regex)  
include_directories(${Boost_INCLUDE_DIR})  
link_directories(${Boost_LIBRARY_DIR})  
target_link_libraries( mp ${Boost_LIBRARIES} )  
  
target_compile_features(mp PUBLIC cxx_std_20)  
add_custom_command(TARGET mp POST_BUILD  
	COMMAND echo "----------------------------------------------"  
	COMMAND ./mp  
	COMMAND echo "----------------------------------------------")  
```
