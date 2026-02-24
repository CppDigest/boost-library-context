# #28 Fix for Ticket #6187 boost::filesystem::exists will thought a string… [Closed]

> Username: alex-87  
> Created at: 2016-05-06 23:51:56 UTC  
> Updated at: 2016-11-24 20:38:43 UTC  
> Closed at: 2016-11-24 20:38:43 UTC  
> Url: https://github.com/boostorg/filesystem/pull/28  

Hello,  
  
  This pull request results from the [Bug Ticket #6187](https://svn.boost.org/trac/boost/ticket/6187). The issue demonstrates the wrong account of the null (not allowed) character in a path, which results in an undefined behavior. The bug is reproducible like following:  
  
```  
#include "boost/filesystem.hpp"  
#include <iostream>  
  
int main(int argc, char *argv[]) {  
  
    std::basic_string<char> path = "/usr/lib/"; // It is admitted that the path exists  
    path.append(2, 0x0); // Adding the 0x00 char twice  
    path.append(3, 'x'); // Adding 'xxx'  
  
    std::cout << path <<  " --- " << boost::filesystem::is_directory(path) << std::endl; //the result is 14 --- 1. It should be 9 --- 0  
    return 0;  
}  
```  
  
The bug fix takes part during the construction of b`oost::filesystem::path` by ensuring the string length is equal to the string size. The `Boost.Filesystem`'s path managment uses `std::basic_string`, which uses the size (i.e.: total number of symbols). The patch obtains the null-terminated string size using `std::char_traits<value_type>::size` method, for `BOOST_ASSERT` purpose into the `boost::filesystem::path`'s constructor, in order to avoid the `0x00` char's consideration in a path name.  
  
Thank you in advance for taking into account this explanation and the pull request,  
Have a great day !

---

## Comment 1

> Username: Beman  
> Created_at: 2016-11-24 20:38:43 UTC  
> Url: https://github.com/boostorg/filesystem/pull/28#issuecomment-262842076  

It isn't the job of class path to decide on validity of strings passed to it.  
  
Asian character encodings may have embedded '\0' characters. Shift-JIS is an example, IIRC. If you know the encoding of a string that is going to be used as a path, you could check it for validity before passing it to a path.  
  
Thanks,  
  
--Beman

---
