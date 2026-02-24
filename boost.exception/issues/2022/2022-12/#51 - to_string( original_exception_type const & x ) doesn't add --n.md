# #51 - to_string( original_exception_type const & x ) doesn't add "\n" [Closed]

> Username: zlojvavan  
> Created at: 2022-12-23 15:36:27 UTC  
> Updated at: 2023-08-05 21:55:47 UTC  
> Closed at: 2023-08-05 21:55:47 UTC  
> Url: https://github.com/boostorg/exception/issues/51  

unlike generalized version to_string( error_info<Tag,T> const & x )  
  
I have my own custom extension to library that besides other features (such as customizing output for error_info_name according to end user dictionary, optionally choosing what types from error_info_map to include/exclude from generated diagnostic info string etc.) fix this annoyance for me but thought you might want to look into this

---

## Comment 1

> Username: zajo  
> Created at: 2022-12-24 01:10:46 UTC  
> Url: https://github.com/boostorg/exception/issues/51#issuecomment-1364429702  

It'd be more annoying to have to remove the `\n` in case it isn't needed.

---

## Comment 2

> Username: zlojvavan  
> Created at: 2022-12-24 08:51:55 UTC  
> Url: https://github.com/boostorg/exception/issues/51#issuecomment-1364490173  

in it's current implementation it defaces generated diagnostic string to some extent  
what are those other scenarios when trailing "\n" would annoy?

---

## Comment 3

> Username: zajo  
> Created at: 2022-12-24 23:14:26 UTC  
> Url: https://github.com/boostorg/exception/issues/51#issuecomment-1364594262  

This looks good to me:  
```  
example.cpp(14): Throw in function main  
Dynamic exception type: boost::wrapexcept<my_error>  
std::exception::what: std::exception  
[my_info1_*] = 1  
[my_info2_*] = 2  
```  
From this source:  
```  
#include <boost/exception/all.hpp>  
#include <iostream>  
  
typedef boost::error_info<struct my_info1_, int> my_info1;  
typedef boost::error_info<struct my_info2_, int> my_info2;  
  
struct my_error: public boost::exception, public std::exception { };  
  
int main()  
{  
    try  
    {  
        BOOST_THROW_EXCEPTION(my_error() << my_info1(1) << my_info2(2));  
    }  
    catch( boost::exception & e )  
    {  
        std::cout << boost::diagnostic_information(e);  
    }  
}  
```

---

## Comment 4

> Username: zlojvavan  
> Created at: 2022-12-26 08:19:19 UTC  
> Url: https://github.com/boostorg/exception/issues/51#issuecomment-1364990486  

use something that involves original_exception_type processing, such as say  
`	try  
	{  
		try  
		{  
			BOOST_THROW_EXCEPTION(my_error() << my_info1(1) << my_info2(2));  
		}  
		catch (...)  
		{  
			boost::rethrow_exception(boost::current_exception());  
		}  
	}  
	catch (boost::exception& e)  
	{  
		std::cout << boost::diagnostic_information(e);  
	}  
`

---

## Comment 5

> Username: zajo  
> Created at: 2023-01-08 00:45:25 UTC  
> Url: https://github.com/boostorg/exception/issues/51#issuecomment-1374672737  

Your code outputs this:  
  
```  
example.cpp(15): Throw in function main  
Dynamic exception type: boost::wrapexcept<my_error>  
std::exception::what: std::exception  
[my_info1_*] = 1  
[my_info2_*] = 2  
```  
  
What is the problem?

---

## Comment 6

> Username: zlojvavan  
> Created at: 2023-01-09 08:04:29 UTC  
> Url: https://github.com/boostorg/exception/issues/51#issuecomment-1375235147  

try this:  
`	try  
	{  
			auto const e = my_error() << my_info1(1) << my_info2(2);  
			e << boost::original_exception_type(&typeid(e));  
			BOOST_THROW_EXCEPTION(e);  
	}  
	catch (boost::exception& e)  
	{  
		std::cout << boost::diagnostic_information(e);  
	}  
`  
  
here's output produced for me (not quite accurate, still struggling with markdown):  
  
> Dynamic exception type: struct boost::wrapexcept<struct my_error>  
>std::exception::what: Unknown exception  
>### **struct my_error[struct my_info1_ * __ptr64] = 1**  
>[struct my_info2_ * __ptr64] = 2
