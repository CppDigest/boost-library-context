# #87 - Infinite recursion error for valid regex [Closed]

> Username: koscelansky  
> Created at: 2019-08-07 18:35:28 UTC  
> Updated at: 2020-01-24 11:19:59 UTC  
> Closed at: 2020-01-24 11:19:59 UTC  
> Url: https://github.com/boostorg/regex/issues/87  

Following code in boost v1.70 will throw exception saying "Encountered an infinite recursion.". But there [should be no problem](https://regex101.com/r/wp4BXe/1). This is the simplest code where I was able to replicate the issue.   
  
```c++  
#include <boost/regex.hpp>  
  
int main()  
{  
	const boost::regex r(R"#((?(DEFINE)(?'a'A)(?'b'(?&a)?(?&a)))(?&b)?)#");  
}  
```  
  
As far as I know, there is an array with already used sub-patterns to prevent infinite looping. It seems like the capture group `b` contains two copies of `a` and this is kind of problematic.
