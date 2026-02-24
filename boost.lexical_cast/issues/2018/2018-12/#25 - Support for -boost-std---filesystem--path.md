# #25 - Support for <boost/std>::filesystem::path [Closed]

> Username: Flamefire  
> Created at: 2018-12-09 17:59:10 UTC  
> Updated at: 2019-06-23 10:34:28 UTC  
> Closed at: 2019-06-23 10:34:28 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/25  

Found during use of boost.program_options: A `lexical_cast` of an unquoted string to `bfs::path` (and probably to `std::path`) stops on the first whitespace making the result invalid.  
  
Reproducer:  
  
std::cout << boost::lexical_cast<boost::filesystem::path>("/home/my user");  
  
Outputs: /home/my     
Expected: /home/my user  
  
Reason is the usage of the stream operators which stop on first whitespace   
  
Possible solution: `path` supports construction from string. So if a ctor from string exists, use that.  
  
More info: http://boost.2283326.n4.nabble.com/program-options-Problem-with-paths-that-have-spaces-td2576490.html

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-12-09 19:12:57 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/25#issuecomment-445563092  

`lexical_cast` should either convert all the data or throw an exception. This looks like a bug, but I'm not sure, what's the right behavior for that case.

---

## Comment 2

> Username: Flamefire  
> Created at: 2018-12-10 08:15:13 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/25#issuecomment-445727600  

What about "if a ctor from string exists, use that." suggested above?

---

## Comment 3

> Username: apolukhin  
> Created at: 2019-06-23 08:21:12 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/25#issuecomment-504730561  

I've just tried the following code  
```  
#include <boost/lexical_cast.hpp>  
#include <boost/filesystem/path.hpp>  
#include <iostream>  
  
int main() {  
    std::cout <<   
      boost::lexical_cast<boost::filesystem::path>("/home/my user");  
  }  
```  
And it throws an exception:  
```  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::bad_lexical_cast> >'  
  what():  bad lexical cast: source type value could not be interpreted as target  
```  
  
Same story with using std::filesystem::path.  
That's a valid behavior.  
  
Adding quotes around the path makes the conversion succeed:  
```  
#include <boost/lexical_cast.hpp>  
#include <filesystem>  
#include <iostream>  
  
int main() {  
    std::cout <<   
      boost::lexical_cast<std::filesystem::path>("\"/home/my user\"");  
  }  
```  
  
Note that outputting a `path` adds quotes:  
```  
#include <boost/lexical_cast.hpp>  
#include <filesystem>  
#include <iostream>  
  
int main() {  
    std::cout <<   
      std::filesystem::path("/home/my user");  
  }  
```  
Outputs: `"/home/my user"`  
  
The existing behavior of the boost::lexical_cast is perfectly right. I'll add tests to make sure that the behavior doesn't change in the future. After that the ticket will be closed.

---

## Comment 4

> Username: apolukhin  
> Created at: 2019-06-23 10:34:28 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/25#issuecomment-504739327  

> What about "if a ctor from string exists, use that." suggested above?  
  
This will break existing code.  
Tests added.
