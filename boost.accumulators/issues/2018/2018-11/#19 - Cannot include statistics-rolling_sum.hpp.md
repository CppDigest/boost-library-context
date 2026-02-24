# #19 - Cannot include statistics/rolling_sum.hpp [Closed]

> Username: jbeich  
> Created at: 2018-11-09 13:50:19 UTC  
> Updated at: 2019-08-05 02:35:02 UTC  
> Closed at: 2019-08-05 02:34:47 UTC  
> Url: https://github.com/boostorg/accumulators/issues/19  

After https://github.com/boostorg/parameter/pull/21 some Accumulators headers no longer compile. Found via [Ceph](https://ptpb.pw/UUBR) ([source](https://github.com/ceph/ceph/blob/b8e4f4eb75a5c7110f680ff8e2bedb3ed33dccd2/src/tools/rbd/action/Bench.cc#L14)). For one, in statistics/rolling_sum.hpp the error originates from https://github.com/boostorg/accumulators/blob/fdabe512f408bd64c415223c01b96da30de69cb1/include/boost/accumulators/statistics/rolling_window.hpp#L30  
  
CC @CromwellEnage  
  
```c++  
$ cat >a.cc  
#include <boost/accumulators/statistics/rolling_sum.hpp>  
  
$ clang++70 -c a.cc  
In file included from a.cc:1:  
In file included from /usr/include/boost/accumulators/statistics/rolling_sum.hpp:13:  
In file included from /usr/include/boost/accumulators/framework/extractor.hpp:25:  
In file included from /usr/include/boost/accumulators/framework/parameters/accumulator.hpp:11:  
/usr/include/boost/parameter/keyword.hpp:53:39: error: no type named 'qualifier' in  
      'boost::accumulators::tag::rolling_window_size_<0>'  
                        typename Tag::qualifier  
                        ~~~~~~~~~~~~~~^~~~~~~~~  
/usr/include/boost/accumulators/statistics/rolling_window.hpp:30:1: note: in instantiation of template class  
      'boost::parameter::keyword<boost::accumulators::tag::rolling_window_size_<0> >' requested here  
BOOST_PARAMETER_NESTED_KEYWORD(tag, rolling_window_size, window_size)  
^  
/usr/include/boost/accumulators/accumulators_fwd.hpp:227:29: note: expanded from macro  
      'BOOST_PARAMETER_NESTED_KEYWORD'  
        ::boost::parameter::keyword<tag_namespace::name>::get();                                    \  
                            ^  
In file included from a.cc:1:  
In file included from /usr/include/boost/accumulators/statistics/rolling_sum.hpp:13:  
In file included from /usr/include/boost/accumulators/framework/extractor.hpp:25:  
In file included from /usr/include/boost/accumulators/framework/parameters/accumulator.hpp:11:  
/usr/include/boost/parameter/keyword.hpp:83:39: error: no type named 'qualifier' in  
      'boost::accumulators::tag::rolling_window_size_<0>'  
                        typename Tag::qualifier  
                        ~~~~~~~~~~~~~~^~~~~~~~~  
/usr/include/boost/parameter/keyword.hpp:109:39: error: no type named 'qualifier' in  
      'boost::accumulators::tag::rolling_window_size_<0>'  
                        typename Tag::qualifier  
                        ~~~~~~~~~~~~~~^~~~~~~~~  
/usr/include/boost/parameter/keyword.hpp:139:39: error: no type named 'qualifier' in  
      'boost::accumulators::tag::rolling_window_size_<0>'  
                        typename Tag::qualifier  
                        ~~~~~~~~~~~~~~^~~~~~~~~  
/usr/include/boost/parameter/keyword.hpp:184:39: error: no type named 'qualifier' in  
      'boost::accumulators::tag::rolling_window_size_<0>'  
                        typename Tag::qualifier  
                        ~~~~~~~~~~~~~~^~~~~~~~~  
/usr/include/boost/parameter/keyword.hpp:214:39: error: no type named 'qualifier' in  
      'boost::accumulators::tag::rolling_window_size_<0>'  
                        typename Tag::qualifier  
                        ~~~~~~~~~~~~~~^~~~~~~~~  
/usr/include/boost/parameter/keyword.hpp:243:39: error: no type named 'qualifier' in  
      'boost::accumulators::tag::rolling_window_size_<0>'  
                        typename Tag::qualifier  
                        ~~~~~~~~~~~~~~^~~~~~~~~  
/usr/include/boost/parameter/keyword.hpp:273:39: error: no type named 'qualifier' in  
      'boost::accumulators::tag::rolling_window_size_<0>'  
                        typename Tag::qualifier  
                        ~~~~~~~~~~~~~~^~~~~~~~~  
8 errors generated.  
```

---

## Comment 1

> Username: CromwellEnage  
> Created at: 2018-11-09 13:59:55 UTC  
> Url: https://github.com/boostorg/accumulators/issues/19#issuecomment-437367559  

I’ll work on a solution later today.  
  
On Fri, Nov 9, 2018 at 08:50 Jan Beich <notifications@github.com> wrote:  
  
> After boostorg/parameter#21  
> <https://github.com/boostorg/parameter/pull/21> some Accumulators headers  
> no longer compile. Found via Ceph <https://ptpb.pw/UUBR> (source  
> <https://github.com/ceph/ceph/blob/b8e4f4eb75a5c7110f680ff8e2bedb3ed33dccd2/src/tools/rbd/action/Bench.cc#L14>).  
> For one, in statistics/rolling_sum.hpp the error originates from  
> https://github.com/boostorg/accumulators/blob/fdabe512f408bd64c415223c01b96da30de69cb1/include/boost/accumulators/statistics/rolling_window.hpp#L30  
>  
> CC @CromwellEnage <https://github.com/CromwellEnage>  
>  
> $ cat >a.cc  
> #include <boost/accumulators/statistics/rolling_sum.hpp>  
>  
> $ clang++70 -c a.cc  
> In file included from a.cc:1:  
> In file included from /usr/include/boost/accumulators/statistics/rolling_sum.hpp:13:  
> In file included from /usr/include/boost/accumulators/framework/extractor.hpp:25:  
> In file included from /usr/include/boost/accumulators/framework/parameters/accumulator.hpp:11:  
> /usr/include/boost/parameter/keyword.hpp:53:39: error: no type named 'qualifier' in  
>       'boost::accumulators::tag::rolling_window_size_<0>'  
>                         typename Tag::qualifier  
>                         ~~~~~~~~~~~~~~^~~~~~~~~  
> /usr/include/boost/accumulators/statistics/rolling_window.hpp:30:1: note: in instantiation of template class  
>       'boost::parameter::keyword<boost::accumulators::tag::rolling_window_size_<0> >' requested hereBOOST_PARAMETER_NESTED_KEYWORD(tag, rolling_window_size, window_size)  
> ^  
> /usr/include/boost/accumulators/accumulators_fwd.hpp:227:29: note: expanded from macro  
>       'BOOST_PARAMETER_NESTED_KEYWORD'  
>         ::boost::parameter::keyword<tag_namespace::name>::get();                                    \  
>                             ^  
> In file included from a.cc:1:  
> In file included from /usr/include/boost/accumulators/statistics/rolling_sum.hpp:13:  
> In file included from /usr/include/boost/accumulators/framework/extractor.hpp:25:  
> In file included from /usr/include/boost/accumulators/framework/parameters/accumulator.hpp:11:  
> /usr/include/boost/parameter/keyword.hpp:83:39: error: no type named 'qualifier' in  
>       'boost::accumulators::tag::rolling_window_size_<0>'  
>                         typename Tag::qualifier  
>                         ~~~~~~~~~~~~~~^~~~~~~~~  
> /usr/include/boost/parameter/keyword.hpp:109:39: error: no type named 'qualifier' in  
>       'boost::accumulators::tag::rolling_window_size_<0>'  
>                         typename Tag::qualifier  
>                         ~~~~~~~~~~~~~~^~~~~~~~~  
> /usr/include/boost/parameter/keyword.hpp:139:39: error: no type named 'qualifier' in  
>       'boost::accumulators::tag::rolling_window_size_<0>'  
>                         typename Tag::qualifier  
>                         ~~~~~~~~~~~~~~^~~~~~~~~  
> /usr/include/boost/parameter/keyword.hpp:184:39: error: no type named 'qualifier' in  
>       'boost::accumulators::tag::rolling_window_size_<0>'  
>                         typename Tag::qualifier  
>                         ~~~~~~~~~~~~~~^~~~~~~~~  
> /usr/include/boost/parameter/keyword.hpp:214:39: error: no type named 'qualifier' in  
>       'boost::accumulators::tag::rolling_window_size_<0>'  
>                         typename Tag::qualifier  
>                         ~~~~~~~~~~~~~~^~~~~~~~~  
> /usr/include/boost/parameter/keyword.hpp:243:39: error: no type named 'qualifier' in  
>       'boost::accumulators::tag::rolling_window_size_<0>'  
>                         typename Tag::qualifier  
>                         ~~~~~~~~~~~~~~^~~~~~~~~  
> /usr/include/boost/parameter/keyword.hpp:273:39: error: no type named 'qualifier' in  
>       'boost::accumulators::tag::rolling_window_size_<0>'  
>                         typename Tag::qualifier  
>                         ~~~~~~~~~~~~~~^~~~~~~~~8 errors generated.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/accumulators/issues/19>, or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AfcJsE_scR-2izCiVIBRRQ9fiItC54jhks5utYgcgaJpZM4YWnKO>  
> .  
>

---

## Comment 2

> Username: CromwellEnage  
> Created at: 2019-02-18 19:53:02 UTC  
> Url: https://github.com/boostorg/accumulators/issues/19#issuecomment-464857014  

PR #23 was accepted and merged and should address this issue.
