# #44 - support for asio standalone [Open]

> Username: ropuls  
> Created at: 2020-12-11 16:41:10 UTC  
> Updated at: 2021-02-07 17:38:50 UTC  
> Url: https://github.com/boostorg/mysql/issues/44  

Hi @anarthal Ruben,  
  
great work, thanks!  
  
There's quite a big community using asio in stand-alone mode without boost. Is boost really needed or would it be possible to make a definition to support compiling without the boost framework and namespaces?  
  
i.e.   
  
  
```  
#ifdef ASIO_STANDALONE  
#define MYSQL_COMPLETION_TOKEN ASIO_COMPLETION_TOKEN_FOR  
#else   
#define MYSQL_COMPLETION_TOKEN BOOST_ASIO_COMPLETION_TOKEN_FOR  
#endif  
  
```  
  
Thanks and best,  
  Roman  
  
  
Update: according to grep, there's just a few includes from boost that are non-asio related, and their functionalities are covered since c++11 by std::*, except - ok - lexical cast, config and endian conversion:  
  
```  
#> egrep -Riohw "#include <boost/.*>"|sort|uniq | grep -v 'boost/asio'  
#include <boost/config.hpp>  
#include <boost/endian/conversion.hpp>  
#include <boost/lexical_cast/try_lexical_convert.hpp>  
#include <boost/optional/optional.hpp>  
#include <boost/system/error_code.hpp>  
#include <boost/system/system_error.hpp>  
#include <boost/utility/string_view.hpp>  
#include <boost/variant2/variant.hpp>  
```

---

## Comment 1

> Username: anarthal  
> Created at: 2020-12-12 13:33:49 UTC  
> Url: https://github.com/boostorg/mysql/issues/44#issuecomment-743756896  

Hi @ropuls, thanks for your time analyzing this. I think the simplest approach to achieve this would be taking the same path as Boost.Json and enabling two modes for the library:  
- A standalone mode that requires C++17 (as variant, optional and string_view require it).  
- A with-Boost mode that requires C++11 and the entire Boost framework.  
  
As you pointed out, there are three libraries that would require consideration:  
- Config is used to determine the availability of some C++14/17 features, adding extra functionality if they are available (e.g. if `std::optional` is available, `boost::mysql::value` gets extra member functions). Requiring C++17 for the standalone mode would suffice.  
- Lexical cast is used in the implementation of the text protocol (what you use when calling `connection::query`). I would have to consider what to use instead - efficiency is important here.  
- Endian is used for integers and floating points. I may be able to implement something myself, but I guess it wouldn't be as efficient as Boost's.  
  
Would the C++17 requirement be okay for your use case?  
  
I can't promise getting this done in the short term as it is a decent amount of work, but I can start considering it before its inclusion in Boost.

---

## Comment 2

> Username: ropuls  
> Created at: 2020-12-12 14:35:10 UTC  
> Url: https://github.com/boostorg/mysql/issues/44#issuecomment-743764881  

hi @anarthal ,  
  
thanks for considering this change. I guess I (and possibly friends) can help out providing code alternatives to the required boost functionalities, mostly re-directing to std::xxx with c++17.  
  
And yes, if c++17 is the minimum language and library level for a standalone version (makes sense to me), one can:   
- clearly make decisions ( ```__has_include ( < filename >``` ) what optional, string_view etc. to use.   
- replace lexical cast with ```std::from_chars``` in most places, which is IMO way more efficient   
- handle endianess conversion with a small drop-in replacement, not that hard - as you said.   
  
Efficiency-wise: I'd opt for making it correct, then optimize if needed. I can hardly imagine that i.e. endianess conversion will have any performance impact in real world scenarios (this is basically a ```std::reverse_copy``` operation instead of a ```std::copy``` ;)  
  
The most annoying part is probably to reorganize namespace declarations and usage. Once a concept is there, some shell scripting can lift major work, but will leave behind some hundred manual fixes before it compiles again. So that part will take some time and patience, but is a one-shot operation. Boost.json and asio's asio/detail/config.hpp are starting points.  
  
Most likely there will be a config.hpp (like in asio) that either globally defines types and namespaces, or do that per type, i.e. have local includes for string_view that branch to the correct type(s).  
  
```  
#ifndef MYSQL_STANDALONE  
namespace boost {  
#endif  
  
namespace mysql {  
#ifdef HAS_STD_STRINGVIEW  
using string_view = std::string_view  
#else  
using string_view = boost::string_view  
#endif  
  
#ifndef MYSQL_STANDALONE  
} // namespace boost  
#endif  
```  
  
If you have the chance to identify and define tasks / TODOs, I'd happily help out and contribute, just let me know where help is needed.  
  
Thanks and best,  
  Roman

---

## Comment 3

> Username: anarthal  
> Created at: 2020-12-13 15:36:44 UTC  
> Url: https://github.com/boostorg/mysql/issues/44#issuecomment-744024895  

Hi @ropuls, thanks for offering your help. I will break down this in more granular issues during this upcoming week and let you know.

---

## Comment 4

> Username: anarthal  
> Created at: 2020-12-26 17:18:21 UTC  
> Url: https://github.com/boostorg/mysql/issues/44#issuecomment-751376458  

Hi @ropuls, sorry for coming back so late. I have created for you the basics for standalone mode (branch `standalone-mode`):  
- I have added an option `BOOST_MYSQL_STANDALONE` to the CMake. By specifying this, the interface library will be built with `-DBOOST_MYSQL_STANDALONE`. Instead of finding Boost, standalone Asio will be searched for. As standalone Asio is not available through standard `find_package`, I have created a CMake finder for it, which gets installed with the library. CMakeLists should not require further changes to support Asio standalone.  
- I have created `boost/mysql/detail/config.hpp`, defining the relevant namespaces and other switches. Standalone library defines stuff in `boost::mysql`, inside `standalone` inline namespace to prevent ODR clashes (as Boost.Json does). Regular mode defines symbols in `boost::mysql` namespace (no inline namespace).  
- I have also created the required files and switches for `string_view`, `optional`, `variant` and `error_code`.  
- I have made all required changes in public headers (although I haven't tested it yet).  
  
What still needs to be done:  
- Apply required changes (switching asio includes/namespace, `string_view`) in implementation files.  
- Remove the dependency to `Boost.Lexical_cast` for standalone mode by implementing the relevant text parsing routines (use `std::from_chars`).  
- Idem for `Boost.Endian`.  
- Apply the required changes throughout the tests.  
- Add documentation on standalone mode (I can do this myself).  
- Add CI builds for standalone mode (I can do this myself).  
  
Some notes for testing:  
- Boost.Mysql runs 3 kinds of tests: unit tests, integration tests and examples. For the two latter, you require an actual database running in localhost. The easiest way to set this up is to use docker, image `anarthal/mysql_8` - have a look to the CI build files for reference.  
- Tests still require Boost, for the unit test framework. This doesn't affect the ability to use the library without Boost.  
  
It would be great if you can submit a PR with the above items. I can take care of documentation and CI builds, so it's okay if you just make tests pass in your local environment.  
  
If you have any questions or comments please feel free to ask.

---

## Comment 5

> Username: ropuls  
> Created at: 2020-12-28 11:58:29 UTC  
> Url: https://github.com/boostorg/mysql/issues/44#issuecomment-751688476  

Hi @anarthal,  
  
no problem - thanks! I made a first test run through the standalone branch to see what items are most problematic. IDK yet if I can really make that compile clean, but I have the following observations:  
  
- lexical_cast: turns out to be a five liner so far with charconv  
- string_view: requires removing of fully qualified namespaces, i.e. string_view instead boost::string_view  
- variant: AFAICS the most edgy part to replace with std::variant, IMO requires some major work-arounds  
- inline namespace ```standalone``` does not work for me with gcc10 since ```detail``` namespaces are ambiguous. I removed it in the first test-run. Needs to be reviewed.  
- value_holder: unable to make that compile with std::string_view  
  
We'd first need to get rid of all ```<boost/...>``` includes and re-direct to local headers that pull in the right stuff.  
  
Also, another issue that I had overseen arises from boost datetime. Either we need to pull in the stand-alone version from here https://github.com/HowardHinnant/date, use the c++20 version (might be good enough, but loosing the c++14/17 world seems wrong), or have another bright idea. If you agree, I'd suggest to pull in date.h from Howard if he's ok with that or make it an external dependency. What's your opinion here?  
  
Regarding further steps: do you plan to change anything in the standalone branch within the near future or is it 'frozen' unless I show up with a PR?  
  
Thanks and best,  
  Roman

---

## Comment 6

> Username: anarthal  
> Created at: 2020-12-28 20:14:21 UTC  
> Url: https://github.com/boostorg/mysql/issues/44#issuecomment-751850333  

Hi @ropuls   
  
- Can you describe what problems arise from using `std::variant`? I was expecting it to be an immediate change, as `boost::variant2` and `std::variant` are designed to have an almost identical interface (Boost.Mysql actually used `std::variant` before `boost::variant2`).  
- I would push towards using the inline namespace in the final version (although the initial workaround you describe seems okay to begin), as it's the approach Boost.Json uses. It can be because some instances of `namespace boost { namespace mysql {` haven't been changed to the `BOOST_MYSQL_NAMESPACE_BEGIN` yet (as I did it only for the public headers).  
- If you can link me the build error for `value_holder` I may be able to aid you.  
- I'm very surprised for issue with `Boost.Datetime`, as Boost.Mysql does *not* use it at any point. It used to employ Howard Hinnant's date, but it was removed because of licensing issues. All datetime manipulations are performed using C++11 `std::chrono` types. The only thing that may be using that is `Boost.Coroutine`, required to run the tests with the `boost::coroutine` completion token. You can try removing these tests by removing the `boost::mysql::test::async_coroutine_errinfo_functions` and `boost::mysql::test::async_coroutine_noerrinfo_functions` network functions (have a look inside `./test/integration/network_functions/`). It should *not* be necessary to use the library though; you can try building any of the examples that do not use Boost coroutines.  
- I'm not planning any immediate changes (this week, at least), so I can consider freezing it while you develop it. Which timing are you considering?  
  
Cheers,  
Ruben.

---

## Comment 7

> Username: ropuls  
> Created at: 2020-12-29 07:35:00 UTC  
> Url: https://github.com/boostorg/mysql/issues/44#issuecomment-751981451  

Good day @anarthal   
  
thanks for your feedback. I will come back to the above points once the major refactoring is done.  
  
Mean-while: I am a bit puzzled about the include statements of your header files which are inside the include folder. When they reference internal (boost/mysql/...) headers, this is done this way:  
  
example file: connection_params.hpp:  
```#include "boost/mysql/detail/config.hpp"```  
  
however, to my understanding, that shall either be  
  
```#include "detail/config.hpp``` <- in a local relative way (we currently stand in boost/mysql), or  
```#include <boost/mysql/detail/config.hpp>``` in an absolute way  
  
I have double checked with other boost libraries which confirms that the second case would be right, imo.  
  
Can you confirm or elaborate that before I start globally replacing include statements?  
  
Thanks,  
  Roman

---

## Comment 8

> Username: anarthal  
> Created at: 2020-12-29 17:41:41 UTC  
> Url: https://github.com/boostorg/mysql/issues/44#issuecomment-752178458  

Hi @ropuls,  
  
I usually use quoted includes to mark project-local inlcudes and bracket includes for system/well established includes. So essentially this is a leftover from when the library was not intended to be a Boost library. I do prefer absolute includes with angle brackets, if you do not mind changing them:  
`#include <boost/mysql/detail/config.hpp>`  
  
Thanks,  
Ruben.

---

## Comment 9

> Username: anarthal  
> Created at: 2021-02-06 20:29:00 UTC  
> Url: https://github.com/boostorg/mysql/issues/44#issuecomment-774538930  

Hi @ropuls,  
  
How are you doing on this issue? Is there anything I can do to help you?  
  
Cheers,  
Ruben.

---

## Comment 10

> Username: ropuls  
> Created at: 2021-02-07 15:02:49 UTC  
> Url: https://github.com/boostorg/mysql/issues/44#issuecomment-774688956  

Hi @anarthal,  
  
thanks for asking! ;) Well, I was able to replace things like lexical cast, endianess etc. and pull in std::string_view and optional, but failed hard with variant and got stuck with thousand of compiler errors in the middle of the transformation. With a recent tremendous lack of time the best I can offer is to provide code parts that replace boost functionalities. However, I sadly won't be able to do the full transmation to non-boost within the near future. If providing parts will help and you'd agree to take the unboostify to your shoulders, we could make some progress though. Let me know and sorry for the poor progress.  
  
Thanks and cheers,  
  Roman

---

## Comment 11

> Username: anarthal  
> Created at: 2021-02-07 17:38:50 UTC  
> Url: https://github.com/boostorg/mysql/issues/44#issuecomment-774715982  

Hi @ropuls,  
  
If you don't mind please share with me your changes up to now and I will see what I can do.  
  
Cheers,  
Ruben.
