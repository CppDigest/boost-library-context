# #151 - text_file_backend, the rotation_size can be doubled at most in ios_base::app mode [Closed]

> Username: grailguo  
> Created at: 2021-06-03 15:33:44 UTC  
> Updated at: 2021-07-19 22:07:45 UTC  
> Closed at: 2021-07-19 22:07:24 UTC  
> Url: https://github.com/boostorg/log/issues/151  

```  
			keywords::open_mode = ios_base::out | ios_base::app,                  // file open mode: append  
			keywords::rotation_size = 16 * 1024,                                  // rotation size, in characters  
```  
  
first time:  
......  
06/03/2021  11:27 PM            16,665 file_20210603_232736_00029.log  
06/03/2021  11:27 PM            16,665 file_20210603_232736_00030.log  
06/03/2021  11:27 PM            16,665 file_20210603_232736_00031.log  
with keywords::enable_final_rotation = false  
06/03/2021  11:27 PM            15,565 file.log  
  
second time:  
......  
06/03/2021  11:27 PM            16,665 file_20210603_232736_00030.log  
06/03/2021  11:27 PM            16,665 file_20210603_232736_00031.log  
**06/03/2021  11:30 PM            32,249 file_20210603_233022_00032.log**  
06/03/2021  11:30 PM            16,686 file_20210603_233022_00033.log  
06/03/2021  11:30 PM            16,686 file_20210603_233022_00034.log  
......

---

## Comment 1

> Username: Lastique  
> Created at: 2021-06-03 15:38:19 UTC  
> Url: https://github.com/boostorg/log/issues/151#issuecomment-853964776  

Please, provide a small reproducer piece of code and describe your platform and compiler.

---

## Comment 2

> Username: Lastique  
> Created at: 2021-06-28 11:23:54 UTC  
> Url: https://github.com/boostorg/log/issues/151#issuecomment-869603075  

Ping?

---

## Comment 3

> Username: wheeler1818  
> Created at: 2021-07-19 18:15:36 UTC  
> Url: https://github.com/boostorg/log/issues/151#issuecomment-882757149  

Hi @Lastique,  
  
i have the same Problem with boost 1.76.0 As you can see the file file.log is ~15.5KB when the application exits. When you start the application a second time, the existing log size is not considered and the log file will not rotate when it's 16KB big. Instead it will rotate when you write another 16KB to the log. Theoretically the file could grow infinitely large, when rotation_size never gets reached during runtime.  
  
Here is a code example for repro:  
  
```  
#include <boost/log/common.hpp>  
#include <boost/log/core.hpp>  
#include <boost/log/sinks/text_ostream_backend.hpp>  
#include <boost/log/sources/logger.hpp>  
#include <boost/log/utility/setup/common_attributes.hpp>  
#include <boost/log/utility/setup/file.hpp>  
  
void init()  
{  
    auto s = boost::log::add_file_log(  
        boost::log::keywords::file_name = "Log/file.log",   
        boost::log::keywords::open_mode = std::ios_base::app,  
        boost::log::keywords::target_file_name = "file_%m%d%Y_%H%M%S_%5N.log",  
        boost::log::keywords::rotation_size = 5 * 1024,   
        boost::log::keywords::auto_flush = true,  
        boost::log::keywords::enable_final_rotation = false);  
  
    auto be = s->locked_backend();  
    be->set_file_collector(boost::log::sinks::file::make_collector(  
        boost::log::keywords::target = "Log",  
        boost::log::keywords::max_files = 10));  
  
    be->scan_for_files();  
}  
  
int main()  
{  
    init();  
    boost::log::sources::logger lg;  
    boost::log::add_common_attributes();  
  
    for (int i(0); i < 1000; ++i)  
    {  
        BOOST_LOG(lg) << "Hello : " << i << std::endl;  
    }  
}  
```  
First Run:  
-rwxrwxrwx 1 fabian fabian 1807 Jul 19 20:09 file.log  
-rwxrwxrwx 1 fabian fabian 5545 Jul 19 20:09 file_07192021_200937_00000.log  
-rwxrwxrwx 1 fabian fabian 5538 Jul 19 20:09 file_07192021_200937_00001.log  
  
Second run:  
-rwxrwxrwx 1 fabian fabian 1807 Jul 19 20:10 file.log  
-rwxrwxrwx 1 fabian fabian 5545 Jul 19 20:09 file_07192021_200937_00000.log  
-rwxrwxrwx 1 fabian fabian 5538 Jul 19 20:09 file_07192021_200937_00001.log  
-rwxrwxrwx 1 fabian fabian **7352** Jul 19 20:10 file_07192021_201038_00002.log  
-rwxrwxrwx 1 fabian fabian 5538 Jul 19 20:10 file_07192021_201038_00003.log  
  
The file file_07192021_201038_00002.log gets too big in this case.  
  
Thanks, Fabian

---

## Comment 4

> Username: Lastique  
> Created at: 2021-07-19 20:42:32 UTC  
> Url: https://github.com/boostorg/log/issues/151#issuecomment-882845882  

Doesn't reproduce on my end with the current develop on Kubuntu 21.04. All the files are 5112 bytes or less.  
  
> When you start the application a second time, the existing log size is not considered and the log file will not rotate when it's 16KB big.  
  
The existing file size is accounted for [here](https://github.com/boostorg/log/blob/affdb2132cc3749b1d1d1907fbdd2c4b5bf42f9e/src/text_file_backend.cpp#L1401), so if that doesn't work then that seems to be a C++ standard library issue. What platform and compiler/standard library are you using?

---

## Comment 5

> Username: Lastique  
> Created at: 2021-07-19 20:52:00 UTC  
> Url: https://github.com/boostorg/log/issues/151#issuecomment-882850860  

Also, could you check if adding `| std::ios_base::ate` to `open_mode` solves the issue for you?

---

## Comment 6

> Username: wheeler1818  
> Created at: 2021-07-19 21:20:13 UTC  
> Url: https://github.com/boostorg/log/issues/151#issuecomment-882868241  

The issue appears under windows with the latest VS 2019 compiler (MSVC 19.29.30038.1). Adding `std::ios_base::ate` fixes the problem for me!

---

## Comment 7

> Username: Lastique  
> Created at: 2021-07-19 22:07:45 UTC  
> Url: https://github.com/boostorg/log/issues/151#issuecomment-882893155  

Thanks.
