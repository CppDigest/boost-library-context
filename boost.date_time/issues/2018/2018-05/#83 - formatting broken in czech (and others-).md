# #83 - formatting broken in czech (and others?) [Open]

> Username: wenottingham  
> Created at: 2018-05-22 13:55:56 UTC  
> Updated at: 2022-03-01 16:16:27 UTC  
> Url: https://github.com/boostorg/date_time/issues/83  

Using boost 1.66.0 (Fedora 28, if that matters)  
  
```  
#include <langinfo.h>  
#include <iostream>  
#include <boost/date_time/gregorian/gregorian.hpp>  
#include <boost/date_time/posix_time/posix_time.hpp>  
#include <boost/date_time/local_time/local_time.hpp>  
#include <boost/regex.hpp>  
  
  
int main(int argc, char **argv) {  
    namespace pt = boost::posix_time;  
    pt::ptime now = pt::second_clock::local_time();  
    using Facet = boost::posix_time::time_facet;  
    std::stringstream ss;  
    char *fmt;  
  
    setlocale(LC_ALL, "");  
    fmt = nl_langinfo(D_FMT);  
  
    auto output_facet(new Facet(fmt));  
    ss.imbue(std::locale(std::locale(), output_facet));  
    ss << now;  
    std::cout << ss.str() << std::endl;  
}  
```  
Compile and run.  
  
```  
[notting@nostromo: ~]$ g++ -Wall -o test_datetime test_datetime.c  -lboost_date_time  
[notting@nostromo: ~]$ LANG=cs_CZ.UTF-8 ./test_datetime   
%-d.%-m.2018  
```  
  
boost is not working properly in the presence of the `-` modifier. It also appears to ignore the `O` and `E` modifiers (used in Persian and other languages), but those at least still substitute ascii numbers properly.

---

## Comment 1

> Username: jeking3  
> Created at: 2018-11-12 13:22:07 UTC  
> Url: https://github.com/boostorg/date_time/issues/83#issuecomment-437877666  

Agree that the format specification you indicated is not supported.  It is not documented as being supported in the [format flags](https://www.boost.org/doc/libs/1_68_0/doc/html/date_time/date_time_io.html#date_time.format_flags), so this would be an enhancement.
