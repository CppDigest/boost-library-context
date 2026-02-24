# #225 - Boost.Locale 1.84.0: boost::locale::to_title() is no longer threadsafe under Windows with ICU [Closed]

> Username: pkl97  
> Created at: 2024-04-02 13:22:55 UTC  
> Updated at: 2024-04-03 11:07:51 UTC  
> Closed at: 2024-04-03 11:07:51 UTC  
> Url: https://github.com/boostorg/locale/issues/225  

The program below runs stable against Boost 1.83.0 but crashes under Boost 1.84.0 when run under Windows together with the ICU library:  
```c++  
#include <iostream>  
#include <vector>  
#include <thread>  
#include <boost/locale/conversion.hpp>  
#include <boost/locale/generator.hpp>  
  
int main()  
{  
    static const std::locale s_theLocale = boost::locale::generator().generate("");  
    const auto& threadFunction = []()  
    {  
        for (unsigned int i = 0;i < 1'000'000;++i)  
            boost::locale::to_title("TEST", s_theLocale);  
    };  
  
    std::vector<std::thread> threads;  
    for (std::size_t i = 1;i <= 5;++i)  
    {  
        threads.emplace_back(threadFunction);  
        std::cout << "Thread " << i << " has been created" << std::endl;  
    }  
    for (std::size_t i = 0;i < threads.size();++i)  
        threads[i].join();  
    std::cout << "Threads have finished" << std::endl;  
  
    return EXIT_SUCCESS;  
}  
```  
  
Boost is compiled with these arguments:  
```bash  
b2.exe --toolset=msvc boost.locale.icu=on boost.locale.iconv=off boost.locale.winapi=off boost.locale.std=off boost.locale.posix=off  
```  
  
We use ICU 74.2 and link it dynamically. Interestingly the problem does not occur with to_upper() and to_lower(), just with to_title().  
  
There was a change in Boost.Locale 1.84.0 regarding to_title() (but not related to ICU according to the changelog). Maybe there is a connection.

---

## Comment 1

> Username: pkl97  
> Created at: 2024-04-02 13:52:58 UTC  
> Url: https://github.com/boostorg/locale/issues/225#issuecomment-2032105478  

After a bit of additional testing I can confirm that the problem also occurs under Linux.

---

## Comment 2

> Username: Flamefire  
> Created at: 2024-04-03 08:18:55 UTC  
> Url: https://github.com/boostorg/locale/issues/225#issuecomment-2033880300  

Indeed this is an issue introduced by a seemingly unrelated change: https://github.com/boostorg/locale/commit/0faa3c10cc929d0803bbac3ec9bfff9093956155#diff-534aadc69c09a9fce955212e657a8638e05aa877b8f753df917303419562f66fL141-R149  
  
I created a fix and hope that I can still include it into the next release.  
  
Thanks for the report!
