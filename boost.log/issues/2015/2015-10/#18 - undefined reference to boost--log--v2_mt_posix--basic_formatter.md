# #18 - undefined reference to boost::log::v2_mt_posix::basic_formatter [Closed]

> Username: Cynede  
> Created at: 2015-10-27 09:44:36 UTC  
> Updated at: 2022-06-23 10:10:14 UTC  
> Closed at: 2015-10-27 09:56:39 UTC  
> Url: https://github.com/boostorg/log/issues/18  

getting this error on linux but everything works on visual studio  
  
``` cmake  
find_package(Boost 1.56.0 REQUIRED COMPONENTS ${USE_Boost_LIBRARIES} QUIET)  
```  
  
getting boost in superbuild with  
  
``` cmake  
list(APPEND _with_boost_libs --with-${USE_Boost_LIBRARIES})  
```  
  
and appealing it to b2  
  
``` c++  
undefined reference to `boost::log::v2_mt_posix::basic_formatter<char> boost::log::v2_mt_posix::parse_formatter<char>(char const*, char const*)'  
```  
  
I'm just including log library with cmake same way as other boost libraries.  
I've found this thread http://sourceforge.net/p/boost-log/discussion/710022/thread/0524c8d3/ but what is boost_log_setup? is it separate boost library?

---

## Comment 1

> Username: Lastique  
> Created at: 2015-10-27 09:56:39 UTC  
> Url: https://github.com/boostorg/log/issues/18#issuecomment-151439500  

Boost.Log builds into two libraries: boost_log and boost_log_setup, the latter depends on the former. The missing function in the linker error is in boost_log_setup. You should add it to the linker command line before boost_log.

---

## Comment 2

> Username: Cynede  
> Created at: 2015-10-27 12:43:30 UTC  
> Url: https://github.com/boostorg/log/issues/18#issuecomment-151479702  

arigato

---

## Comment 3

> Username: autosquid  
> Created at: 2015-11-04 03:51:11 UTC  
> Updated at: 2015-11-04 03:51:32 UTC  
> Url: https://github.com/boostorg/log/issues/18#issuecomment-153563061  

@Heather  I think I met the same problem, could I have a look at your related cmake file?

---

## Comment 4

> Username: Cynede  
> Created at: 2015-11-04 08:00:33 UTC  
> Url: https://github.com/boostorg/log/issues/18#issuecomment-153626110  

@autosquid I've used to add this library manually after compiling during linking   
`list(APPEND Boost_LIBRARIES boost_log_setup)`

---

## Comment 5

> Username: autosquid  
> Created at: 2015-11-04 09:56:34 UTC  
> Url: https://github.com/boostorg/log/issues/18#issuecomment-153665375  

@Heather  no luck for me. As far as I see, cmake finds the right dylib of  `log_setup`, but still the link error exists in my case. The same code works for boost 1.58.0, but fails for 1.59.0

---

## Comment 6

> Username: autosquid  
> Created at: 2015-11-04 09:58:19 UTC  
> Url: https://github.com/boostorg/log/issues/18#issuecomment-153665832  

The link error appears once I add the call of `logging::add_file_log`

---

## Comment 7

> Username: Lastique  
> Created at: 2015-11-04 10:07:48 UTC  
> Url: https://github.com/boostorg/log/issues/18#issuecomment-153668599  

There was a bug in 1.59 that has been fixed for 1.60. See https://github.com/boostorg/log/pull/10.

---

## Comment 8

> Username: autosquid  
> Created at: 2015-11-04 10:14:52 UTC  
> Url: https://github.com/boostorg/log/issues/18#issuecomment-153671063  

@Lastique  OMG! Then what I can do to get around if I am installing 1.59 from homebrew? do a patch?

---

## Comment 9

> Username: Lastique  
> Created at: 2015-11-04 10:29:27 UTC  
> Url: https://github.com/boostorg/log/issues/18#issuecomment-153674440  

You will have to build a patched version of Boost. I'm not sure how it works with homebrew, I'm not a Mac user.

---

## Comment 10

> Username: autosquid  
> Created at: 2015-11-04 10:35:09 UTC  
> Url: https://github.com/boostorg/log/issues/18#issuecomment-153676615  

@Lastique  thanks, I'll patch it according to the P.R. you provide.

---

## Comment 11

> Username: jspam  
> Created at: 2022-06-23 10:10:14 UTC  
> Url: https://github.com/boostorg/log/issues/18#issuecomment-1164225369  

For anyone searching for the error message: this also happens if Boost.Log has been built with `BOOST_LOG_WITHOUT_SETTINGS_PARSERS`. In that case, `boost_log_setup` is a stub and does not contain `parse_formatter`.
