# #157 - from_time_t(t1 + t2) gains incorrect and different results on Windows and Linux [Open]

> Username: andry81  
> Created at: 2020-06-01 05:03:39 UTC  
> Updated at: 2020-06-18 13:18:30 UTC  
> Url: https://github.com/boostorg/date_time/issues/157  

OS: `Windows 7 x64 `  
Compilers: `MSVC 2017 15.9.19`, `GCC 4.9.3`  
boost: `1.72.0`  
  
    #include <boost/date_time/posix_time/posix_time.hpp>  
  
    namespace boost_ptime = boost::posix_time;  
  
    std::istringstream iss1{ "%H:%M:%S" };  
    iss1.imbue(std::locale(std::locale::classic(), new boost_ptime::time_input_facet("07:00:00")));  
    boost_ptime::ptime time_value1;  
    iss1 >> time_value1;  
  
    std::time_t t1 = 0;  
    if (!time_value1.is_not_a_date_time()) {  
        t1 = boost_ptime::to_time_t(time_value1);  
    }  
  
    std::istringstream iss2{ "%Y-%m-%d" };  
    iss2.imbue(std::locale(std::locale::classic(), new boost_ptime::time_input_facet("2019-06-17")));  
    boost_ptime::ptime time_value2;  
    iss2 >> time_value2;  
  
    std::time_t t2 = 0;  
    if (!time_value2.is_not_a_date_time()) {  
        t2 = boost_ptime::to_time_t(time_value2);  
    }  
  
    std::ostringstream oss;  
    oss.imbue(std::locale(std::locale::classic(), new boost_ptime::time_facet("%d.%m.%y %H:%M:%S")));  
    boost_ptime::ptime time_value3 = boost_ptime::from_time_t(t1 + t2);  
    oss << time_value3;  
  
    if (!oss.fail()) {  
        printf("%s\n", oss.str().c_str());  
    }  
  
On Windows it prints:  
  
```  
27.10.18 03:46:08  
```  
  
On Linux it crashes (sometimes not, but gains a different result):  
  
```  
terminate called after throwing an instance of 'boost::wrapexcept<boost::gregorian::bad_year>'  
  what():  Year is out of valid range: 1400..9999  
```  
  
The same code over a standard library (`std::get_time`, `std::put_time`) works as expected.

---

## Comment 1

> Username: JeffGarland  
> Created at: 2020-06-01 14:21:05 UTC  
> Url: https://github.com/boostorg/date_time/issues/157#issuecomment-636888638  

hmm, well the code isn't asking to do the correct thing is part of the issue.  
```  
std::istringstream iss1{ "%H:%M:%S" };  
iss1.imbue(std::locale(std::locale::classic(), new boost_ptime::time_input_facet("07:00:00")));  
boost_ptime::ptime time_value1;  
```  
ptime needs the full year-month-day to construct.  So really the input here should be to a time_duration and not a ptime -- i think we're already in undefined behavior -- so the exception seems about right to me.  Not sure what it's doing on windows that lets it churn forward.

---

## Comment 2

> Username: andry81  
> Created at: 2020-06-01 15:12:02 UTC  
> Url: https://github.com/boostorg/date_time/issues/157#issuecomment-636914654  

> ptime needs the full year-month-day to construct. So really the input here should be to a time_duration and not a ptime  
  
I don't quite get it. Are you saying I have to preparse the input string to find out what I have to use  it (ptime or time_duration)? Why the std get_time does not require it? I've tried to use boost to simplify the process of conversion when you are suggest to complicate it.

---

## Comment 3

> Username: JeffGarland  
> Created at: 2020-06-01 15:27:32 UTC  
> Url: https://github.com/boostorg/date_time/issues/157#issuecomment-636923113  

A ptime represents a unique point in time, so it nees YYYY-MM-DD HH-MM-SS.  In the first block of code the input parser is only provide with HH-MM-SS -- so it can't construct a ptime correctly.  A time_duration type, however, is effectively HH-MM-SS -- so that could be retrieved from the facet specification as written.

---

## Comment 4

> Username: andry81  
> Created at: 2020-06-01 15:57:32 UTC  
> Url: https://github.com/boostorg/date_time/issues/157#issuecomment-636938580  

I don't know the time type in the first place. I have a format compatible to the `std::get_time` format + serialized time string. How you suppose to workaround that?

---

## Comment 5

> Username: JeffGarland  
> Created at: 2020-06-01 16:10:45 UTC  
> Url: https://github.com/boostorg/date_time/issues/157#issuecomment-636954204  

Maybe you just want ptime_from_tm then?  
  
tm pt_tm;  
pt_tm.tm_year = 105;  
pt_tm.tm_mon  = 0;  
pt_tm.tm_mday = 1;  
pt_tm.tm_hour = 1;  
pt_tm.tm_min  = 2;  
pt_tm.tm_sec  = 3;  
ptime pt = ptime_from_tm(pt_tm);  
// pt => 2005-Jan-01 01:02:03  
  
https://www.boost.org/doc/libs/1_73_0/doc/html/date_time/posix_time.html#ptime_struct_tm

---

## Comment 6

> Username: andry81  
> Created at: 2020-06-01 17:18:22 UTC  
> Updated at: 2020-06-18 13:18:30 UTC  
> Url: https://github.com/boostorg/date_time/issues/157#issuecomment-636996536  

> Maybe you just want ptime_from_tm then?  
  
It is not compatible with the std either.  
I had to make an ugly workaround for that:  
  
msvc 19.00.23506: https://rextester.com/PHSPAM46089  
gcc 4.9.3, 5.4.0 + clang 3.4.1: https://godbolt.org/z/AcXQB9  
  
Gcc `4.9.3` does not have `std::get_time`, so the workaround uses `boost::posix_time` instead which is not compatible with the `std::get_time` because uses `strftime` time format strings.  
  
For example, one of incompatibilities is with the `%y` placeholder, where for the `std::get_time` it expands into 2 ranges:  
* [00-68] -> [2000-2068]  
* [69-99] -> [1900-1969]  
  
But for the  `strftime`:  
* [00-99] -> [2000-2099]  
  
```  
#include <string>  
#include <ctime>  
#include <chrono>  
#include <iomanip>  
#include <stdexcept>  
#include <sstream>  
#include <algorithm>  
#include <functional>  
  
#include <string.h>  
#include <stdarg.h>  
#include <stdio.h>  
#include <inttypes.h>  
  
#if defined(_MSC_VER)  
#define UTILITY_COMPILER_CXX_MSC  
#define UTILITY_COMPILER_CXX_VERSION _MSC_VER  
#elif defined(__clang__)  
#define UTILITY_COMPILER_CXX_CLANG  
#define UTILITY_COMPILER_CXX_VERSION __clang_major__  
#define UTILITY_COMPILER_CXX_VERSION_MINOR __clang_minor__  
#elif defined(__GNUC__)  
#define UTILITY_COMPILER_CXX_GCC  
#define UTILITY_COMPILER_CXX_VERSION __GNUC__  
#define UTILITY_COMPILER_CXX_VERSION_MINOR __GNUC_MINOR__  
#endif  
  
#if defined(UTILITY_COMPILER_CXX_MSC) && UTILITY_COMPILER_CXX_VERSION >= 1600 || \  
    defined(UTILITY_COMPILER_CXX_GCC) && UTILITY_COMPILER_CXX_VERSION >= 5 || \  
    defined(UTILITY_COMPILER_CXX_CLANG) && (UTILITY_COMPILER_CXX_VERSION > 3 || UTILITY_COMPILER_CXX_VERSION == 3 && UTILITY_COMPILER_CXX_VERSION_MINOR >= 3)  
#   define UTILITY_PLATFORM_FEATURE_STD_HAS_GET_TIME  
#endif  
  
// switch to the boost implementation to avoid exception on negative time_t values in gmtime* functions  
#if defined(UTILITY_COMPILER_CXX_MSC) && UTILITY_COMPILER_CXX_VERSION < 1910  
#define UTILITY_PLATFORM_FEATURE_USE_BOOST_POSIX_TIME_INSTEAD_STD_GET_TIME  
#endif  
  
#if !defined(UTILITY_PLATFORM_FEATURE_STD_HAS_GET_TIME) || defined(UTILITY_PLATFORM_FEATURE_USE_BOOST_POSIX_TIME_INSTEAD_STD_GET_TIME)  
#include <boost/date_time/posix_time/posix_time.hpp>  
#endif  
  
namespace utility {  
namespace time  
{  
    size_t get_leap_days(size_t year)  
    {  
        const size_t prev_year = year - 1;  
        return prev_year / 4 - prev_year / 100 + prev_year / 400;  
    }  
  
    std::tm gmtime(const std::time_t & time)  
    {  
        std::tm t = {};  
  
#ifdef UTILITY_COMPILER_CXX_MSC  
        gmtime_s(&t, &time);  
#else  
        gmtime_r(&time, &t);  
#endif  
  
        return t;  
    }  
  
    std::time_t timegm(const std::tm & time)  
    {  
        std::tm c_tm = time; // must be not constant  
  
#ifdef UTILITY_COMPILER_CXX_MSC  
        return ::_mkgmtime(&c_tm);  
#elif defined(UTILITY_COMPILER_CXX_CLANG) || defined(UTILITY_COMPILER_CXX_GCC)  
        return ::timegm(&c_tm);  
#else  
#   error platform is not implemented  
#endif  
    }  
  
#ifdef UTILITY_PLATFORM_FEATURE_STD_HAS_GET_TIME  
    template <class t_elem, class t_traits, class t_alloc>  
    bool get_time(std::tm & time, const std::string & locale,  
                            const std::basic_string<t_elem, t_traits, t_alloc> & fmt, const std::basic_string<t_elem, t_traits, t_alloc> & date_time_str,  
                            std::exception * exception_ptr = nullptr)  
    {  
        if (exception_ptr) {  
            *exception_ptr = std::exception{};  
        }  
  
        time = std::tm{};  
  
        std::basic_istringstream<t_elem, t_traits, t_alloc> ss{ date_time_str };  
        if (!locale.empty()) {  
            ss.imbue(std::locale(locale));  
        }  
  
        // CAUTION:  
        //  t.tm_yday can be not initialized here!  
        //  
        ss >> std::get_time(&time, fmt.c_str());  
  
        return !ss.fail();  
    }  
  
    bool get_time(std::string & time_str, const std::string & fmt, const std::tm & time, std::exception * exception_ptr = nullptr)  
    {  
        if (exception_ptr) {  
            *exception_ptr = std::exception{};  
        }  
  
        std::stringstream buffer;  
        buffer << std::put_time(&time, fmt.c_str());  
        if (!buffer.fail()) {  
            time_str = buffer.str();  
            return true;  
        }  
  
        return false;  
    }  
#endif  
  
    using convert_time_string_to_std_time_error_func_t      = std::function<void(const std::string &, const std::string &, const std::exception *)>;  
    using convert_time_string_from_std_time_error_func_t    = std::function<void(const std::string &, const std::time_t &, const std::exception *)>;  
  
    std::time_t convert_time_string_to_std_time(const std::string & time_str_format, const std::string & time_value_str,  
                                                convert_time_string_to_std_time_error_func_t error_func = convert_time_string_to_std_time_error_func_t{})  
    {  
        std::time_t epoch_time_sec = 0;  
  
#if defined(UTILITY_PLATFORM_FEATURE_STD_HAS_GET_TIME) && !defined(UTILITY_PLATFORM_FEATURE_USE_BOOST_POSIX_TIME_INSTEAD_STD_GET_TIME)  
        try  
        {  
            std::tm t;  
            if (utility::time::get_time(t, std::string{}, time_str_format, time_value_str)) {  
                if (time_str_format.find("%Y") == std::string::npos) {  
                    if (time_str_format.find("%y") == std::string::npos) {  
                        // from 1900 year  
                        t.tm_year += 70;  
                    }  
                    else {  
                        // [00-68] -> 2000-2068  
                        // [69-99] -> 1969-1999  
                        if (t.tm_year < 69) {  
                            t.tm_year += 100;  
                        }  
                    }  
                }  
                if (!t.tm_mday) t.tm_mday = 1;  
  
#if defined(UTILITY_COMPILER_CXX_MSC) && UTILITY_COMPILER_CXX_VERSION < 1910  
                time_t epoch_time_correction_sec = 0;  
                int epoch_time_correction_from_year = 70;  
                if (t.tm_year < 70) {  
                    // CAUTION:  
                    //   utility::time::timegm will fail if the year component is less than 70, so fix the year and add delta directly to the time_t  
                    //  
                    epoch_time_correction_from_year = t.tm_year;  
                    t.tm_year = 70;  
                }  
#endif  
  
                errno = 0; // must be to ensure the state change  
                epoch_time_sec = utility::time::timegm(t);  
                if (epoch_time_sec == -1 && errno != 0) { // error handle  
                    epoch_time_sec = 0;  
                }  
                else {  
#if defined(UTILITY_COMPILER_CXX_MSC) && UTILITY_COMPILER_CXX_VERSION < 1910  
                    for (int i = epoch_time_correction_from_year; i < 70; i++) {  
                        epoch_time_sec -= 365 * 24 * 60 * 60;  
                    }  
                    epoch_time_sec -= (get_leap_days(1970) - get_leap_days(1900 + epoch_time_correction_from_year)) * 24 * 60 * 60;  
#endif  
                }  
            }  
            else {  
                if (error_func) {  
                    error_func(time_str_format, time_value_str, nullptr);  
                }  
            }  
        }  
#else  
        try  
        {  
            namespace boost_ptime = boost::posix_time;  
  
            struct base_time_extractor  
            {  
                base_time_extractor() {  
                    std::istringstream iss{ "%Y-%m-%d" };  
                    iss.imbue(std::locale(std::locale::classic(), new boost_ptime::time_input_facet("1970-01-01")));  
                    boost_ptime::ptime base_ptime_value;  
                    iss >> base_ptime_value;  
                    base_time = boost_ptime::to_tm(base_ptime_value);  
                    //printf("base tm_year (boost) 1: %i\n", base_time.tm_year);  
                }  
  
                std::tm base_time;  
            } static s_base_time_extractor;  
  
            std::istringstream iss{ time_value_str };  
            iss.imbue(std::locale(std::locale::classic(), new boost_ptime::time_input_facet(time_str_format.c_str())));  
            boost_ptime::ptime ptime_value;  
            iss >> ptime_value;  
  
            if (!ptime_value.is_not_a_date_time()) {  
                std::tm t = boost_ptime::to_tm(ptime_value);  
  
                if (time_str_format.find("%Y") == std::string::npos) {  
                    if (time_str_format.find("%y") == std::string::npos) {  
                        // biased base, normalize  
                        t.tm_year -= s_base_time_extractor.base_time.tm_year;  
                        // from 1900 year  
                        t.tm_year += 70;  
                    }  
                    else {  
                        // CAUTION:  
                        //  Here is a specific boost incompatability versus the std::get_time because the boost has used the  
                        //  strftime format which is quite different:  
                        //  [00-99] -> 2000-2099  
  
                        // std::get_time format, just in case:  
                        //  [00-68] -> 2000-2068  
                        //  [69-99] -> 1969-1999  
                        if (t.tm_year < 69) {  
                            t.tm_year += 100;  
                        }  
                    }  
                }  
                if (!t.tm_mday) t.tm_mday = 1; // just in case  
  
                epoch_time_sec = utility::time::timegm(t);  
            }  
            else {  
                if (error_func) {  
                    error_func(time_str_format, time_value_str, nullptr);  
                }  
            }  
        }  
        catch (const boost::bad_lexical_cast & ex)  
        {  
            if (error_func) {  
                error_func(time_str_format, time_value_str, &ex);  
            }  
        }  
#endif  
        catch (const std::out_of_range & ex)  
        {  
            if (error_func) {  
                error_func(time_str_format, time_value_str, &ex);  
            }  
        }  
  
        return epoch_time_sec;  
    }  
  
    std::string convert_time_string_from_std_time(const std::string & time_str_format, const std::time_t & time_value,  
                                                  convert_time_string_from_std_time_error_func_t error_func = convert_time_string_from_std_time_error_func_t{})  
    {  
#if defined(UTILITY_PLATFORM_FEATURE_STD_HAS_GET_TIME) && !defined(UTILITY_PLATFORM_FEATURE_USE_BOOST_POSIX_TIME_INSTEAD_STD_GET_TIME)  
        std::string time_str;  
  
        try  
        {  
#if !defined(UTILITY_COMPILER_CXX_MSC) || UTILITY_COMPILER_CXX_VERSION >= 1910  
            std::tm t = utility::time::gmtime(time_value);  
#else  
            std::tm t = utility::time::gmtime(time_value >= 0 ? time_value : 0); // avoid exception code 0xc0000409  
#endif  
  
            if (time_str_format.find("%Y") == std::string::npos) {  
                if (time_str_format.find("%y") == std::string::npos) {  
                    // from 1900 year  
                    t.tm_year += 70;  
                }  
                else {  
                    // std::get_time format:  
                    //  [00-68] -> 2000-2068  
                    //  [69-99] -> 1969-1999  
                    if (t.tm_year < 69) {  
                        t.tm_year += 100;  
                    }  
                }  
            }  
            if (!t.tm_mday) t.tm_mday = 1; // just in case  
  
            if (utility::time::get_time(time_str, time_str_format, t)) {  
                return time_str;  
            }  
            else {  
                if (error_func) {  
                    error_func(time_str_format, time_value, nullptr);  
                }  
            }  
        }  
#else  
        try  
        {  
            namespace boost_ptime = boost::posix_time;  
  
            std::tm t = utility::time::gmtime(time_value);  
  
            if (!t.tm_mday) t.tm_mday = 1; // just in case  
  
            std::ostringstream oss;  
            oss.imbue(std::locale(std::locale::classic(), new boost_ptime::time_facet(time_str_format.c_str())));  
            boost_ptime::ptime ptime_value = boost_ptime::ptime_from_tm(t);  
            oss << ptime_value;  
  
            if (!oss.fail()) {  
                return oss.str();  
            }  
            else {  
                if (error_func) {  
                    error_func(time_str_format, time_value, nullptr);  
                }  
            }  
        }  
        catch (const boost::bad_lexical_cast & ex)  
        {  
            if (error_func) {  
                error_func(time_str_format, time_value, &ex);  
            }  
        }  
#endif  
        catch (const std::out_of_range & ex)  
        {  
            if (error_func) {  
                error_func(time_str_format, time_value, &ex);  
            }  
        }  
  
#if defined(UTILITY_PLATFORM_FEATURE_STD_HAS_GET_TIME) && !defined(UTILITY_PLATFORM_FEATURE_USE_BOOST_POSIX_TIME_INSTEAD_STD_GET_TIME)  
        return time_str;  
#else  
        return std::string{};  
#endif  
    }  
  
}  
}  
  
int main()  
{  
    {  
        const std::time_t t1 = utility::time::convert_time_string_to_std_time("%Y-%m-%d %H:%M:%S", "1970-01-01 07:00:00");  
        const std::time_t t2 = utility::time::convert_time_string_to_std_time("%Y-%m-%d %H:%M:%S", "2019-05-17 00:00:00");  
      
        const std::string t = utility::time::convert_time_string_from_std_time("%d.%m.%Y %H:%M:%S", t1 + t2);  
      
        printf("t1: %" PRId64 "\n", t1);  
        printf("t2: %" PRId64 "\n", t2);  
        printf("time: %s\n", t.c_str());  
    }  
    {  
        const std::time_t t1 = utility::time::convert_time_string_to_std_time("%H:%M:%S", "07:00:00");  
        const std::time_t t2 = utility::time::convert_time_string_to_std_time("%Y-%m-%d", "2019-05-17");  
      
        const std::string t = utility::time::convert_time_string_from_std_time("%d.%m.%Y %H:%M:%S", t1 + t2);  
      
        printf("t1: %" PRId64 "\n", t1);  
        printf("t2: %" PRId64 "\n", t2);  
        printf("time: %s\n", t.c_str());  
    }  
    {  
        const std::time_t t1 = utility::time::convert_time_string_to_std_time("%H:%M:%S", "07:00:00");  
        const std::time_t t2 = utility::time::convert_time_string_to_std_time("%y-%m-%d", "68-05-17");  
      
        const std::string t = utility::time::convert_time_string_from_std_time("%d.%m.%Y %H:%M:%S", t1 + t2);  
      
        printf("t1: %" PRId64 "\n", t1);  
        printf("t2: %" PRId64 "\n", t2);  
        printf("time: %s\n", t.c_str());  
    }  
    {  
        const std::time_t t1 = utility::time::convert_time_string_to_std_time("%H:%M:%S", "07:00:00");  
        const std::time_t t2 = utility::time::convert_time_string_to_std_time("%y-%m-%d", "69-05-17");  
      
        const std::string t = utility::time::convert_time_string_from_std_time("%d.%m.%Y %H:%M:%S", t1 + t2);  
      
        printf("t1: %" PRId64 "\n", t1);  
        printf("t2: %" PRId64 "\n", t2);  
        printf("time: %s\n", t.c_str());  
    }  
    {  
        const std::time_t t1 = utility::time::convert_time_string_to_std_time("%H:%M:%S", "07:00:00");  
        const std::time_t t2 = utility::time::convert_time_string_to_std_time("%y-%m-%d", "70-05-17");  
      
        const std::string t = utility::time::convert_time_string_from_std_time("%d.%m.%Y %H:%M:%S", t1 + t2);  
      
        printf("t1: %" PRId64 "\n", t1);  
        printf("t2: %" PRId64 "\n", t2);  
        printf("time: %s\n", t.c_str());  
    }  
}  
```  
  
**gcc 4.9.3**  
```  
t1: 25200  
t2: 1558051200  
time: 17.05.2019 07:00:00  
t1: 25200  
t2: 1558051200  
time: 17.05.2019 07:00:00  
t1: 25200  
t2: 3104438400  
time: 17.05.2068 07:00:00  
t1: 25200  
t2: 3135974400  
time: 17.05.2069 07:00:00  
t1: 25200  
t2: 3167510400  
time: 17.05.2070 07:00:00  
```  
  
**gcc 5.4.0**  
```  
t1: 25200  
t2: 1558051200  
time: 17.05.2019 07:00:00  
t1: 25200  
t2: 1558051200  
time: 17.05.2019 07:00:00  
t1: 25200  
t2: 3104438400  
time: 17.05.2068 07:00:00  
t1: 25200  
t2: -19785600  
time: 17.05.1969 07:00:00  
t1: 25200  
t2: 11750400  
time: 17.05.1970 07:00:00  
```  
  
**clang 3.4.1**  
```  
t1: 25200  
t2: 1558051200  
time: 17.05.2019 07:00:00  
t1: 25200  
t2: 1558051200  
time: 17.05.2019 07:00:00  
t1: 25200  
t2: 3104438400  
time: 17.05.2068 07:00:00  
t1: 25200  
t2: -19785600  
time: 17.05.1969 07:00:00  
t1: 25200  
t2: 11750400  
time: 17.05.1970 07:00:00  
```  
  
PS:  
Was quite hard to completely workaround that because of many points with unexpected behaviour.
