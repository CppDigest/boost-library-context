# #160 - suppress -Wformat-overflow when optimization is enabled on GCC 8.2.0 [Closed]

> Username: ToruNiina  
> Created at: 2018-09-12 09:36:56 UTC  
> Updated at: 2019-02-08 17:25:42 UTC  
> Closed at: 2019-02-08 13:41:43 UTC  
> Url: https://github.com/boostorg/test/issues/160  

Hi,  
  
When I compile the following code with g++ v8.2.0 on linux (Ubuntu 16.04),  
  
```cpp  
#define BOOST_TEST_MODULE test_min  
#include <boost/test/included/unit_test.hpp>  
  
BOOST_AUTO_TEST_CASE(minimal)  
{  
    const double a = 1.5, b = 2.5, c = 4.0;  
    BOOST_TEST(c == a + b, boost::test_tools::tolerance(1e-8));  
}  
```  
  
I get this warning.  
  
```console  
$ g++-8 test.cpp -Wformat-overflow=1 -O2 -I./boost_1_68_0  
In file included from ./boost_1_68_0/boost/test/impl/compiler_log_formatter.ipp:29,  
                 from ./boost_1_68_0/boost/test/included/unit_test.hpp:18,  
                 from test.cpp:2:  
./boost_1_68_0/boost/test/utils/setcolor.hpp: In constructor ‘boost::unit_test::utils::scope_setcolor::scope_setcolor(std::ostream&, boost::unit_test::utils::term_attr::_, boost::unit_test::utils::term_color::_, boost::unit_test::utils::term_color::_)’:  
./boost_1_68_0/boost/test/utils/setcolor.hpp:84:38: warning: ‘%d’ directive writing between 1 and 11 bytes into a region of size between 0 and 9 [-Wformat-overflow=]  
         m_command_size = std::sprintf( m_control_command, "%c[%d;%d;%dm", 0x1B, attr, fg + 30, bg + 40 );  
                          ~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
./boost_1_68_0/boost/test/utils/setcolor.hpp:84:38: note: directive argument in the range [-2147483618, 2147483647]  
./boost_1_68_0/boost/test/utils/setcolor.hpp:84:38: note: directive argument in the range [-2147483608, 2147483647]  
./boost_1_68_0/boost/test/utils/setcolor.hpp:84:38: note: ‘sprintf’ output between 9 and 39 bytes into a destination of size 13  
```  
  
And the version of g++ is this.  
  
```console  
$ g++-8 --version  
g++ (GCC) 8.2.0  
Copyright (C) 2018 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
```  
  
Also, I found that it happens only when I turn optimization on. With `-O0` (or without `-O` flags), I found no warning with `-Wformat-overflow=1`. You can check it [here](https://wandbox.org/permlink/vZgFd3aIsBtPec63).  
  
I know in reality it does not matter because the types of arguments are actually enum types and the range of the defined value is in [0, 9]. Even though, I would like to suppress it.  
  
As the message said, the reason for this warning is that the buffer is too short. To supress the warning, basically, we need to increase the buffer size, or use `std::snprintf` instead. However `std::snprintf` doesn't exist in C++98, so we cannot use it. I think expanding the buffer is the best way if the size of the class `setcolor` does not matter.  
  
I have one more workaround that does not need to expand the buffer. Assuming ASCII, we can convert an integral value to ASCII code and put it exactly as a byte. To make the argument as just one character, we can write `3` and `4` directly in the format string instead of adding `30` and `40` to the argument.  
  
This workaround would look like this. I confirmed that it suppresses the warning.  
  
```cpp  
m_command_size = std::sprintf( m_control_command, "%c[%c;3%c;4%cm", 0x1B,  
    static_cast<char>(attr + 48), static_cast<char>(fg + 48), static_cast<char>(bg + 48));  
```  
  
However, since POSIX does not define the character encoding, it is not a portable code.  
  
What do you think about this problem?  
  
Thanks.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-11-06 19:51:06 UTC  
> Url: https://github.com/boostorg/test/issues/160#issuecomment-436385655  

Thanks for the report, and sorry about the delay. I'll try to integrate the fix for the upcoming release.

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2019-02-05 00:05:00 UTC  
> Url: https://github.com/boostorg/test/issues/160#issuecomment-460463025  

Should be in 1.70, thanks!

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2019-02-08 13:41:43 UTC  
> Url: https://github.com/boostorg/test/issues/160#issuecomment-461806305  

In master, closing. Thanks!
