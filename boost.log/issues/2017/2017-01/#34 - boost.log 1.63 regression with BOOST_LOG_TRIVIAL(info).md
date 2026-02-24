# #34 - boost.log 1.63 regression with BOOST_LOG_TRIVIAL(info) [Closed]

> Username: ahundt  
> Created at: 2017-01-10 22:30:30 UTC  
> Updated at: 2017-01-11 15:45:40 UTC  
> Closed at: 2017-01-11 15:45:40 UTC  
> Url: https://github.com/boostorg/log/issues/34  

I'm on boost 1.63 as installed by homebrew on OS X 10.11.6 and Xcode Version 8.2.1 (8C1002). I'm getting a crash [on the following line available at this link](https://github.com/ahundt/grl/blob/cbd96f576deb49722c598119f838fdece0bad8d2/src/v_repExtGrlCisstInverseKinematics/v_repExtGrlInverseKinematics.cpp#L188):  
  
```  
    BOOST_LOG_TRIVIAL(info) << "Inverse Kinematics plugin initialized. Build date/time: " << __DATE__ << " " << __TIME__ <<"\n";  
```  
  
This worked correctly on previous versions of Boost, and I'm building my source using cmake's xcode generator. I tested reverting back to boost 1.60 and this code runs correctly, so I believe it is due to a regression between boost 1.60 and boost 1.63. I also tried building 1.63 from source and was able to reproduce the crash.  
  
Test I ran to switch libraries:  
```  
brew install boost  
# clean, recompile, run, test crashed  
brew reinstall boost --build-from-source  
# clean, recompile, run, test crashed  
brew uninstall boost  
brew install boost160  
brew link boost160 --force  
# clean, recompile, run, test succeeded  
```  
  
Stack Trace:  
```  
#0	0x00007fff8d680981 in std::__1::basic_ostream<char, std::__1::char_traits<char> >::sentry::sentry(std::__1::basic_ostream<char, std::__1::char_traits<char> >&) ()  
#1	0x000000012234ea9d in boost::log::v2_mt_posix::basic_formatting_ostream<char, std::__1::char_traits<char>, std::__1::allocator<char> >::sentry::sentry(boost::log::v2_mt_posix::basic_formatting_ostream<char, std::__1::char_traits<char>, std::__1::allocator<char> >&) at /usr/local/include/boost/log/utility/formatting_ostream.hpp:127  
#2	0x000000012234e5cd in boost::log::v2_mt_posix::basic_formatting_ostream<char, std::__1::char_traits<char>, std::__1::allocator<char> >::sentry::sentry(boost::log::v2_mt_posix::basic_formatting_ostream<char, std::__1::char_traits<char>, std::__1::allocator<char> >&) at /usr/local/include/boost/log/utility/formatting_ostream.hpp:128  
#3	0x000000012234e442 in boost::log::v2_mt_posix::basic_formatting_ostream<char, std::__1::char_traits<char>, std::__1::allocator<char> >::formatted_write(char const*, long) at /usr/local/include/boost/log/utility/formatting_ostream.hpp:630  
#4	0x000000012234e3fc in boost::log::v2_mt_posix::basic_formatting_ostream<char, std::__1::char_traits<char>, std::__1::allocator<char> >::operator<<(char const*) at /usr/local/include/boost/log/utility/formatting_ostream.hpp:403  
#5	0x0000000122327b18 in boost::log::v2_mt_posix::aux::enable_if_record_ostream<boost::log::v2_mt_posix::basic_record_ostream<char>, boost::log::v2_mt_posix::basic_record_ostream<char>&>::type boost::log::v2_mt_posix::operator<<<boost::log::v2_mt_posix::basic_record_ostream<char>, char [57]>(boost::log::v2_mt_posix::basic_record_ostream<char>&, char const (&) [57]) at /usr/local/include/boost/log/sources/record_ostream.hpp:203  
#6	0x0000000122320374 in ::v_repStart(void *, int) at /Users/athundt/source/robonetracker/modules/grl/src/v_repExtGrlInverseKinematics/v_repExtGrlInverseKinematics.cpp:188  
#7	0x00000001023efa0c in CPlugin::load() ()  
#8	0x00000001023f05c8 in CPluginContainer::addPlugin(char const*, char const*) ()  
#9	0x00000001023946a8 in CUiThread::__executeCommandViaUiThread(SUIThreadCommand*, SUIThreadCommand*) ()  
#10	0x00000001023945f2 in CUiThread::executeCommandViaUiThread(SUIThreadCommand*, SUIThreadCommand*) ()  
#11	0x000000010224d964 in simLoadModule_internal(char const*, char const*) ()  
#12	0x000000010000203a in loadPlugin(char const*, char const*) ()  
#13	0x000000010000262c in simulatorInit() ()  
#14	0x00000001023bd2a8 in App::_runInitializationCallback(void (*)()) ()  
#15	0x00000001023bd439 in App::run(void (*)(), void (*)(), void (*)()) ()  
#16	0x000000010223a047 in simRunSimulator_internal(char const*, int, void (*)(), void (*)(), void (*)()) ()  
#17	0x0000000100003d2c in main ()  
#18	0x0000000100001fa4 in start ()  
```  
  
Also of note, when I compile I define:  
`ADD_DEFINITIONS(-DBOOST_LOG_DYN_LINK)`

---

## Comment 1

> Username: Lastique  
> Created at: 2017-01-10 23:02:49 UTC  
> Url: https://github.com/boostorg/log/issues/34#issuecomment-271726470  

I cannot reproduce the crash with the modified `trivial` example from the library source (with the `main` function having only the logging statement you quoted). Tried with gcc 6.2 and clang 3.8.1 on Linux. I can't test on OS X as I don't have access to this platform. Please, provide a self-contained reduced repro that shows the crash.  
  
Also, from the stacktrace it looks like some plugin loading is involved. If that's the case, please note that the library does not support unloading libraries that are in any way involved in logging, including adding attributes or sinks and emitting log records. Is it possible that that is the problem?

---

## Comment 2

> Username: ahundt  
> Created at: 2017-01-11 00:20:13 UTC  
> Url: https://github.com/boostorg/log/issues/34#issuecomment-271740289  

All I use is `BOOST_LOG_TRIVIAL()` with the various info, warning, error parameters but nothing else. Does that involve adding attributes or sinks and emitting log records?  
  
The code I'm running is in fact loaded from a plugin, though I only load plugins, I never unload them.   
  
This is probably going to be unhelpful but some standard boilerplate did work:  
https://gist.github.com/ahundt/db4677f71b6fa892d86c651776fa6d0b  
  
Perhaps I should just go with std::cout, haha...

---

## Comment 3

> Username: Lastique  
> Created at: 2017-01-11 09:27:02 UTC  
> Url: https://github.com/boostorg/log/issues/34#issuecomment-271819675  

> Does that involve adding attributes or sinks and emitting log records?  
  
It does. Though if you're not unloading libraries, that should not cause problems. One other idea is perhaps there are several different versions of the library that are linked in the application and loaded plugins, or the library is linked statically in some places. This can cause ABI incompatibility issues or multiple versions of library singletons in the process. Anyway, I can't be of much help without a repro.

---

## Comment 4

> Username: ahundt  
> Created at: 2017-01-11 15:45:30 UTC  
> Url: https://github.com/boostorg/log/issues/34#issuecomment-271903450  

okay thanks anyway! I'll just close this since I'm only using very basic functionality and I can work around this issue by just using the standard library.
