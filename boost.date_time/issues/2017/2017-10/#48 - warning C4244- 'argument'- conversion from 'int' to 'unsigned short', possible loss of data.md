# #48 - warning C4244: 'argument': conversion from 'int' to 'unsigned short', possible loss of data [Closed]

> Username: viboes  
> Created at: 2017-10-14 11:01:43 UTC  
> Updated at: 2017-11-05 13:49:20 UTC  
> Closed at: 2017-11-05 13:49:20 UTC  
> Url: https://github.com/boostorg/date_time/issues/48  

```  
C:\projects\boost-root\boost/date_time/adjust_functors.hpp(105): warning C4244: 'argument': conversion from 'int' to 'unsigned short', possible loss of data  
C:\projects\boost-root\boost/date_time/adjust_functors.hpp(93): note: while compiling class template member function 'boost::gregorian::date_duration boost::date_time::month_functor<boost::gregorian::greg_durations_config::date_type>::get_neg_offset(const date_type &) const'  
        with  
        [  
            date_type=boost::gregorian::greg_durations_config::date_type  
        ]  
C:\projects\boost-root\boost/date_time/date_duration_types.hpp(184): note: see reference to function template instantiation 'boost::gregorian::date_duration boost::date_time::month_functor<boost::gregorian::greg_durations_config::date_type>::get_neg_offset(const date_type &) const' being compiled  
        with  
        [  
            date_type=boost::gregorian::greg_durations_config::date_type  
        ]  
C:\projects\boost-root\boost/date_time/date_duration_types.hpp(188): note: see reference to class template instantiation 'boost::date_time::month_functor<boost::gregorian::greg_durations_config::date_type>' being compiled  
C:\projects\boost-root\boost/date_time/date_duration_types.hpp(187): note: while compiling class template member function 'boost::gregorian::date_duration boost::date_time::years_duration<boost::gregorian::greg_durations_config>::get_offset(const boost::gregorian::date &) const'  
C:\projects\boost-root\boost/date_time/posix_time/date_duration_operators.hpp(77): note: see reference to function template instantiation 'boost::gregorian::date_duration boost::date_time::years_duration<boost::gregorian::greg_durations_config>::get_offset(const boost::gregorian::date &) const' being compiled  
C:\projects\boost-root\boost/date_time/posix_time/date_duration_operators.hpp(77): note: see reference to class template instantiation 'boost::date_time::years_duration<boost::gregorian::greg_durations_config>' being compiled  
C:\projects\boost-root\boost/date_time/adjust_functors.hpp(76): warning C4244: 'argument': conversion from 'int' to 'unsigned short', possible loss of data  
C:\projects\boost-root\boost/date_time/adjust_functors.hpp(64): note: while compiling class template member function 'boost::gregorian::date_duration boost::date_time::month_functor<boost::gregorian::greg_durations_config::date_type>::get_offset(const date_type &) const'  
        with  
        [  
            date_type=boost::gregorian::greg_durations_config::date_type  
        ]  
C:\projects\boost-root\boost/date_time/date_duration_types.hpp(189): note: see reference to function template instantiation 'boost::gregorian::date_duration boost::date_time::month_functor<boost::gregorian::greg_durations_config::date_type>::get_offset(const date_type &) const' being compiled  
        with  
        [  
            date_type=boost::gregorian::greg_durations_config::date_type  
        ]  
compile-c-c++ ..\..\..\bin.v2\libs\thread\build\msvc-14.1\debug\link-static\threadapi-win32\threading-multi\win32\tss_pe.obj  
  
```

---

## Comment 1

> Username: eldiener  
> Created at: 2017-10-14 14:53:47 UTC  
> Url: https://github.com/boostorg/date_time/issues/48#issuecomment-336639949  

I updated a fix for this warning on the 'develop' branch.

---

## Comment 2

> Username: mclow  
> Created at: 2017-10-14 15:41:02 UTC  
> Updated at: 2017-10-14 15:41:37 UTC  
> Url: https://github.com/boostorg/date_time/issues/48#issuecomment-336643190  

Wait - Vicente, were you building from the release branch or master?  
I thought that this was already fixed on develop.

---

## Comment 3

> Username: viboes  
> Created at: 2017-10-14 17:08:47 UTC  
> Updated at: 2017-10-14 17:09:37 UTC  
> Url: https://github.com/boostorg/date_time/issues/48#issuecomment-336649289  

develop  
See https://github.com/boostorg/thread/blob/883637e0f4fbe58d97211fe79d8022ef68b6245e/appveyor.yml#L59  
  
You can see the results here  
https://ci.appveyor.com/project/viboes/thread/build/1.0.152-feature/timespec_clocks

---

## Comment 4

> Username: Lastique  
> Created at: 2017-10-25 19:12:41 UTC  
> Url: https://github.com/boostorg/date_time/issues/48#issuecomment-339439517  

See https://github.com/boostorg/date_time/pull/50.
