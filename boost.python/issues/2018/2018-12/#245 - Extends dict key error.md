# #245 - Extends dict key error [Open]

> Username: StevenBaby  
> Created at: 2018-12-18 10:06:28 UTC  
> Updated at: 2018-12-18 10:06:28 UTC  
> Url: https://github.com/boostorg/python/issues/245  

I have written two snippets, with cpp and python  
  
```c++  
#include <iostream>  
#include <boost/python.hpp>  
#include <boost/python/dict.hpp>  
  
using namespace boost::python;  
  
struct testdict : public boost::python::dict {  
	testdict()  
	{  
		std::cout << "constructor" << std::endl;  
	}  
  
	virtual ~testdict()  
	{  
		std::cout << "destructor" << std::endl;  
	}  
};  
  
BOOST_PYTHON_MODULE(boost) {  
	class_<testdict, bases<dict>>("testdict");  
};  
```  
  
```python  
#!/usr/bin/python3  
# coding=utf-8  
  
import boost  
  
data = boost.testdict()  
  
data['key'] = 1  
  
print(data)  
print('key' in data)  
```  
  
compile cpp code and run python code the output is not expected  
  
```  
constructor  
{'key': 1}  
False  
```
