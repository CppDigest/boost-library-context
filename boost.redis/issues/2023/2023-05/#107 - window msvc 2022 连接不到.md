# #107 - window msvc 2022 连接不到 [Closed]

> Username: wanglei-fenyu  
> Created at: 2023-05-30 12:34:49 UTC  
> Updated at: 2023-06-02 06:36:13 UTC  
> Closed at: 2023-06-02 06:36:13 UTC  
> Url: https://github.com/boostorg/redis/issues/107  

为啥window vs2022编译会链接不到  他只有头文件啊

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-05-30 21:06:37 UTC  
> Url: https://github.com/boostorg/redis/issues/107#issuecomment-1569103531  

Hi, you have to include `#include <boost/redis/src.hpp>` in one of your header files, for example where you define the main function.
