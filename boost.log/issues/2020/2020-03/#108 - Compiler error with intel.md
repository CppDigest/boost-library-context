# #108 - Compiler error with intel [Closed]

> Username: Thunder1982  
> Created at: 2020-03-13 10:23:18 UTC  
> Updated at: 2020-03-13 10:56:01 UTC  
> Closed at: 2020-03-13 10:56:00 UTC  
> Url: https://github.com/boostorg/log/issues/108  

Hello,  
  
I have trouble to compile the following minimal example using actual 1.72 Boost Log with the intel compiler  
### icpc version 19.0.5.281 (gcc version 4.8.5 compatibility)  
  
  
```  
#include "boost/log/trivial.hpp"  
  
using namespace std;  
  
int main(int argc, char** argv){  
  BOOST_LOG_TRIVIAL(error) << "Hello world";  
  
  return 0;  
}  
```  
This results in the following compile error:  
  
```  
icpc    -c -g -I/opt/tools/boost/boost-1.72.0/include -std=c++11 -MMD -MP -MF "build/Debug/Intel-Linux/main.o.d" -o build/Debug/Intel-Linux/main.o main.cpp  
/opt/tools/boost/boost-1.72.0/include/boost/log/sources/severity_feature.hpp(252): error: more than one operator "|" matches these operands:  
            function template "boost::enable_if<boost::mpl::eval_if<boost::is_same<boost::log::v2_mt_posix::keywords::tag::severity::qualifier, boost::parameter::forward_reference>, boost::mpl::if_<boost::is_const<Default>, boost::mpl::false_, boost::mpl::true_>, boost::mpl::false_>::type, boost::parameter::aux::default_<boost::log::v2_mt_posix::keywords::tag::severity, Default>>::type boost::parameter::keyword<Tag>::operator|(Default &) const [with  
                      Tag=boost::log::v2_mt_posix::keywords::tag::severity]"  
            function template "boost::enable_if<boost::mpl::eval_if<boost::is_scalar<Default>, boost::mpl::false_, boost::mpl::eval_if<boost::is_same<boost::log::v2_mt_posix::keywords::tag::severity::qualifier, boost::parameter::consume_reference>, boost::mpl::true_, boost::mpl::if_<boost::is_same<boost::log::v2_mt_posix::keywords::tag::severity::qualifier, boost::parameter::forward_reference>, boost::mpl::true_, boost::mpl::false_>>>::type,  
                      boost::parameter::aux::default_r_<boost::log::v2_mt_posix::keywords::tag::severity, Default>>::type boost::parameter::keyword<Tag>::operator|(Default &&) const [with Tag=boost::log::v2_mt_posix::keywords::tag::severity]"  
            operand types are: const boost::parameter::keyword<boost::log::v2_mt_posix::keywords::tag::severity> | boost::log::v2_mt_posix::trivial::severity_level  
          m_SeverityAttr.set_value(args[keywords::severity | m_DefaultSeverity]);  
                                                           ^  
          detected during:  
            instantiation of "boost::log::v2_mt_posix::record boost::log::v2_mt_posix::sources::basic_severity_logger<BaseT, LevelT>::open_record_unlocked(const ArgsT &) [with BaseT=boost::log::v2_mt_posix::sources::basic_logger<char, boost::log::v2_mt_posix::sources::severity_logger_mt<boost::log::v2_mt_posix::trivial::severity_level>, boost::log::v2_mt_posix::sources::multi_thread_model<boost::log::v2_mt_posix::aux::light_rw_mutex>>, LevelT=boost::log::v2_mt_posix::trivial::severity_level,  
                      ArgsT=boost::parameter::aux::tagged_argument<boost::log::v2_mt_posix::keywords::tag::severity, const boost::log::v2_mt_posix::trivial::severity_level>]" at line 459 of "/opt/tools/boost/boost-1.72.0/include/boost/log/sources/basic_logger.hpp"  
            instantiation of "boost::log::v2_mt_posix::record boost::log::v2_mt_posix::sources::basic_composite_logger<CharT, FinalT, ThreadingModelT, FeaturesT>::open_record(const ArgsT &) [with CharT=char, FinalT=boost::log::v2_mt_posix::sources::severity_logger_mt<boost::log::v2_mt_posix::trivial::severity_level>, ThreadingModelT=boost::log::v2_mt_posix::sources::multi_thread_model<boost::log::v2_mt_posix::aux::light_rw_mutex>,  
                      FeaturesT=boost::log::v2_mt_posix::sources::features<boost::log::v2_mt_posix::sources::severity<boost::log::v2_mt_posix::trivial::severity_level>>, ArgsT=boost::parameter::aux::tagged_argument<boost::log::v2_mt_posix::keywords::tag::severity, const boost::log::v2_mt_posix::trivial::severity_level>]" at line 6 of "main.cpp"  
  
compilation aborted for main.cpp (code 2)  
  
```  
  
With gcc everything is fine. It seems that changes came with 1.71 lead to this.  
  
Thanks

---

## Comment 1

> Username: Lastique  
> Created at: 2020-03-13 10:56:00 UTC  
> Url: https://github.com/boostorg/log/issues/108#issuecomment-598665451  

This is an issue with Boost.Parameter, please report it [there](https://github.com/boostorg/parameter/issues/new).
