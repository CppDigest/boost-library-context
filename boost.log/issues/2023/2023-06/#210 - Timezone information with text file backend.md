# #210 - Timezone information with text file backend [Open]

> Username: KennethBuijssen  
> Created at: 2023-06-07 15:20:02 UTC  
> Updated at: 2023-10-09 18:17:35 UTC  
> Url: https://github.com/boostorg/log/issues/210  

We're currently working on adding logging to our application and trying to get a log file in the following format: `test_20230607T165634+0200.log`  
  
With the following code however, the resulting log file is `test_20230607T165634.log`. After reading the documentation is seems that boost log does not support any of the time zone flags, since it uses ptime internally (https://www.boost.org/doc/libs/1_82_0/doc/html/date_time/date_time_io.html#date_time.format_flags). Could support for this be added?  
  
```  
#include <boost/log/trivial.hpp>  
#include <boost/log/utility/setup/file.hpp>  
  
void init_logger(const std::string& filename)  
{  
  auto sink = boost::log::add_file_log(boost::log::keywords::file_name = filename + "_%Y%m%dT%H%M%S%q.log");  
}  
  
int main(int, char**)  
{  
  init_logger("test");  
  
  BOOST_LOG_TRIVIAL(info) << "Hello, World!";  
}  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2023-07-02 11:31:22 UTC  
> Url: https://github.com/boostorg/log/issues/210#issuecomment-1616611533  

This is not easy to do, since Boost.DateTime does not allow to create a time zone object that reflects the time zone set on the system. There doesn't seem to be a portable way to obtain this information either, at least I'm not aware of one. I reported this to Boost.DateTime, but the chances for this to be implemented soon are rather slim, unfortunately.

---

## Comment 2

> Username: otopetrik  
> Created at: 2023-10-09 18:17:35 UTC  
> Url: https://github.com/boostorg/log/issues/210#issuecomment-1753451550  

It could be useful to have configuration option to use UTC time for file names.  
  
Instead of having log file names with times in *unknown* time zone, the timezone would be known.  
Especially useful if already using `attributes::utc_clock` for `TimeStamp`  
  
Looks like `local_time_traits::get_clock()` is hardcoded, with no way to use `utc_time_traits`  
https://github.com/boostorg/log/blob/ae9bf80017b881ca7b1575093ec98cf130619492/src/text_file_backend.cpp#L242
