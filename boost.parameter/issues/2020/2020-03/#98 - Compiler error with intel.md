# #98 - Compiler error with intel [Open]

> Username: Thunder1982  
> Created at: 2020-03-13 14:51:47 UTC  
> Updated at: 2022-02-14 09:18:51 UTC  
> Url: https://github.com/boostorg/parameter/issues/98  

Hello,  
  
I`ve posted the issue before in Boost.Log but was redirected to this project.  
  
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

> Username: Thunder1982  
> Created at: 2020-09-02 08:48:26 UTC  
> Url: https://github.com/boostorg/parameter/issues/98#issuecomment-685453131  

Does anybody know what's the cause of this problem and how to fix this issue?  
It prevents to use boost logging for all users of intel compilers.

---

## Comment 2

> Username: Lastique  
> Created at: 2020-09-02 11:06:10 UTC  
> Url: https://github.com/boostorg/parameter/issues/98#issuecomment-685631152  

Since Intel compiler is untested and not freely available, I doubt the problem will be fixed any time soon, unless someone comes up with a patch.

---

## Comment 3

> Username: Lastique  
> Created at: 2020-09-02 11:18:48 UTC  
> Url: https://github.com/boostorg/parameter/issues/98#issuecomment-685649182  

As an idea to try, could someone test if changing [boost/parameter/keyword.hpp:434](https://github.com/boostorg/parameter/blob/9fc61ba8ff48eb3065e3455afe8148c81622316a/include/boost/parameter/keyword.hpp#L434) from this:  
  
```  
::boost::is_scalar<Default>  
```  
to this helps:  
```  
::boost::mpl::and_<::boost::is_scalar<Default>, ::boost::mpl::not_<::boost::is_reference<Default> > >  
```  
  
You may also need to add these includes at the beginning of the file:  
  
```  
#include <boost/mpl/and.hpp>  
#include <boost/mpl/not.hpp>  
#include <boost/type_traits/is_reference.hpp>  
```

---

## Comment 4

> Username: brettonidas  
> Created at: 2020-11-10 13:21:39 UTC  
> Url: https://github.com/boostorg/parameter/issues/98#issuecomment-724697834  

> Since Intel compiler is untested and not freely available, I doubt the problem will be fixed any time soon, unless someone comes up with a patch.  
  
I believe the Intel compiler is free to developers contributing to open-source projects.  
  
[Intel: Qualify for Free Software](https://software.intel.com/content/www/us/en/develop/articles/qualify-for-free-software.html?wapkw=%28driver%29)

---

## Comment 5

> Username: ezralanglois  
> Created at: 2021-01-23 22:36:31 UTC  
> Url: https://github.com/boostorg/parameter/issues/98#issuecomment-766192963  

> ::boost::mpl::and_<::boost::is_scalar<Default>, ::boost::mpl::not_<::boost::is_reference<Default> > >  
  
I have the same issue and this did not resolve it. I am using Boost 1.74 and Intel 2019.

---

## Comment 6

> Username: ezralanglois  
> Created at: 2021-01-24 18:01:27 UTC  
> Url: https://github.com/boostorg/parameter/issues/98#issuecomment-766403755  

I experimented with the code. The problem looks to be that `is_scalar` is false. So the fix above is short circuited.  
  
The parameter for Default in this case is a class, so I think that makes sense. I don't see any override for is_scalar with the class `secuirty_level` or its super class `attribute`

---

## Comment 7

> Username: ezralanglois  
> Created at: 2021-01-25 02:30:30 UTC  
> Url: https://github.com/boostorg/parameter/issues/98#issuecomment-766499718  

The only workaround I found so far is to define `BOOST_PARAMETER_DISABLE_PERFECT_FORWARDING`.   
  
This in combination with upgrading to Intel 19.0.4.20190416

---

## Comment 8

> Username: ezralanglois  
> Created at: 2021-01-25 02:58:08 UTC  
> Url: https://github.com/boostorg/parameter/issues/98#issuecomment-766507548  

I forgot to mention I defined `BOOST_PARAMETER_DISABLE_MP11_USAGE` too. This alone did not work. I am not sure if both are necessary.

---

## Comment 9

> Username: ezralanglois  
> Created at: 2021-01-25 17:16:04 UTC  
> Url: https://github.com/boostorg/parameter/issues/98#issuecomment-766971507  

Also, if you want to test with the intel compiler, this would be a good place to start:   
https://github.com/oneapi-src/oneapi-ci/pull/30  
  
Setting up tests requires some admin level permissions, but I am happy to help someone who has that set this up.

---

## Comment 10

> Username: Thunder1982  
> Created at: 2022-02-14 09:18:51 UTC  
> Url: https://github.com/boostorg/parameter/issues/98#issuecomment-1038843314  

I can confirm that the workaround:  
`#define BOOST_PARAMETER_DISABLE_PERFECT_FORWARDING`  
solves the issue for me.  
Thank you very much
