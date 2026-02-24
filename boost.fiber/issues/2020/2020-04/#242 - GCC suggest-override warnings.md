# #242 - GCC suggest-override warnings [Closed]

> Username: EugeneZelenko  
> Created at: 2020-04-12 19:08:35 UTC  
> Updated at: 2022-09-18 14:45:05 UTC  
> Closed at: 2022-09-18 14:45:05 UTC  
> Url: https://github.com/boostorg/fiber/issues/242  

Complete list of warnings when Boost 1.72 is built with GCC 7.5 with `-Wsuggest-override` added to `cxxflags`:  
  
<pre>  
./boost/fiber/algo/round_robin.hpp:48:18: warning: ‘virtual void boost::fibers::algo::round_robin::awakened(boost::fibers::context*)’ can be marked override [-Wsuggest-override]  
./boost/fiber/algo/round_robin.hpp:50:23: warning: ‘virtual boost::fibers::context* boost::fibers::algo::round_robin::pick_next()’ can be marked override [-Wsuggest-override]  
./boost/fiber/algo/round_robin.hpp:52:18: warning: ‘virtual bool boost::fibers::algo::round_robin::has_ready_fibers() const’ can be marked override [-Wsuggest-override]  
./boost/fiber/algo/round_robin.hpp:54:18: warning: ‘virtual void boost::fibers::algo::round_robin::suspend_until(const time_point&)’ can be marked override [-Wsuggest-override]  
./boost/fiber/algo/round_robin.hpp:56:18: warning: ‘virtual void boost::fibers::algo::round_robin::notify()’ can be marked override [-Wsuggest-override]  
./boost/fiber/algo/shared_work.hpp:62:10: warning: ‘virtual void boost::fibers::algo::shared_work::awakened(boost::fibers::context*)’ can be marked override [-Wsuggest-override]  
./boost/fiber/algo/shared_work.hpp:64:15: warning: ‘virtual boost::fibers::context* boost::fibers::algo::shared_work::pick_next()’ can be marked override [-Wsuggest-override]  
./boost/fiber/algo/shared_work.hpp:66:10: warning: ‘virtual bool boost::fibers::algo::shared_work::has_ready_fibers() const’ can be marked override [-Wsuggest-override]  
./boost/fiber/algo/shared_work.hpp:71:7: warning: ‘virtual void boost::fibers::algo::shared_work::suspend_until(const time_point&)’ can be marked override [-Wsuggest-override]  
./boost/fiber/algo/shared_work.hpp:73:7: warning: ‘virtual void boost::fibers::algo::shared_work::notify()’ can be marked override [-Wsuggest-override]  
./boost/fiber/algo/work_stealing.hpp:65:18: warning: ‘virtual void boost::fibers::algo::work_stealing::awakened(boost::fibers::context*)’ can be marked override [-Wsuggest-override]  
./boost/fiber/algo/work_stealing.hpp:67:23: warning: ‘virtual boost::fibers::context* boost::fibers::algo::work_stealing::pick_next()’ can be marked override [-Wsuggest-override]  
./boost/fiber/algo/work_stealing.hpp:73:18: warning: ‘virtual bool boost::fibers::algo::work_stealing::has_ready_fibers() const’ can be marked override [-Wsuggest-override]  
./boost/fiber/algo/work_stealing.hpp:77:18: warning: ‘virtual void boost::fibers::algo::work_stealing::suspend_until(const time_point&)’ can be marked override [-Wsuggest-override]  
./boost/fiber/algo/work_stealing.hpp:79:18: warning: ‘virtual void boost::fibers::algo::work_stealing::notify()’ can be marked override [-Wsuggest-override]  
libs/fiber/src/context.cpp:33:36: warning: unused parameter ‘c’ [-Wunused-parameter]  
libs/fiber/src/future.cpp:14:25: warning: ‘virtual const char* boost::fibers::future_error_category::name() const’ can be marked override [-Wsuggest-override]  
libs/fiber/src/future.cpp:18:34: warning: ‘virtual std::error_condition boost::fibers::future_error_category::default_error_condition(int) const’ can be marked override [-Wsuggest-override]  
libs/fiber/src/future.cpp:42:18: warning: ‘virtual bool boost::fibers::future_error_category::equivalent(const std::error_code&, int) const’ can be marked override [-Wsuggest-override]  
libs/fiber/src/future.cpp:47:25: warning: ‘virtual std::__cxx11::string boost::fibers::future_error_category::message(int) const’ can be marked override [-Wsuggest-override]  
</pre>  
  
`BOOST_OVERRIDE` was introduced in https://github.com/boostorg/config/commit/ffe4e0f5a448578cce14e3eed0cf7163333a81d9.

---

## Comment 1

> Username: olk  
> Created at: 2020-04-24 09:49:02 UTC  
> Url: https://github.com/boostorg/fiber/issues/242#issuecomment-618915405  

could you provide a patch please
