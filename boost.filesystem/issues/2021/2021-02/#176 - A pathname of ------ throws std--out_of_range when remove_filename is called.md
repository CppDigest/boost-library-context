# #176 - A pathname of "////" throws std::out_of_range when remove_filename is called [Closed]

> Username: bshastry  
> Created at: 2021-02-18 15:21:53 UTC  
> Updated at: 2021-02-26 11:55:06 UTC  
> Closed at: 2021-02-26 11:54:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/176  

```  
#include <boost/filesystem.hpp>  
  
int main()  
{  
        boost::filesystem::path r("////");  
        r.remove_filename();  
        return 0;  
}  
```  
  
compiled via clang/gcc  
  
```  
$ clang++ /tmp/test.cpp -lboost_filesystem -o test  
$ ./test  
terminate called after throwing an instance of 'std::out_of_range'  
  what():  basic_string::erase: __pos (which is 18446744073709551615) > this->size() (which is 4)  
Aborted (core dumped)  
```  
  
Pathnames with `/` appended to the one in the title also lead to a similar throw.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-02-26 11:55:06 UTC  
> Url: https://github.com/boostorg/filesystem/issues/176#issuecomment-786602407  

Thanks for the report.
