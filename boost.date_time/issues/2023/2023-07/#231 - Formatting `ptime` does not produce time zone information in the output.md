# #231 - Formatting `ptime` does not produce time zone information in the output [Open]

> Username: Lastique  
> Created at: 2023-07-02 14:46:22 UTC  
> Updated at: 2023-07-02 15:32:03 UTC  
> Url: https://github.com/boostorg/date_time/issues/231  

When the time type is `boost::posix_time::ptime`, Boost.DateTime output facets don't produce any output for "%Z", "%z", "%Q" and "%q" flags.  
  
Here is an example:  
  
```  
#include <ctime>  
#include <iostream>  
#include <locale>  
#include <boost/date_time.hpp>  
  
static const char format[] = "%Y-%m-%d %H:%M:%S %z";  
  
int main()  
{  
    auto *facet = new boost::posix_time::time_facet(format);  
    std::locale loc(std::locale(), facet);  
    std::locale::global(loc);  
    std::cout.imbue(loc);  
  
    auto now = boost::posix_time::microsec_clock::local_time();  
  
    std::cout << '"' << now << '"' << std::endl;  
  
    std::time_t time_t_now = boost::posix_time::to_time_t(now);  
    std::tm tm_buf = {};  
    std::tm* tm_now = boost::date_time::c_time::localtime(&time_t_now, &tm_buf);  
  
    char buf[128];  
    std::strftime(buf, sizeof(buf), format, tm_now);  
    std::cout << '"' << buf << '"' << std::endl;  
}  
```  
  
Output:  
  
```  
"2023-07-02 17:36:16"  
"2023-07-02 20:36:16 +0300"  
```  
  
I would expect the time zone format flags to work and result in the current system time zone in the output.
