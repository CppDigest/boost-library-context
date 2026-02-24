# #264 - boost/thread/detail/move.hpp causes compilation failures [Closed]

> Username: apolukhin  
> Created at: 2019-01-08 08:08:05 UTC  
> Updated at: 2019-03-17 15:17:30 UTC  
> Closed at: 2019-03-17 15:17:30 UTC  
> Url: https://github.com/boostorg/thread/issues/264  

Test for boost::variant https://github.com/boostorg/variant/blob/46d8b92f73db38651a212521ff2d7c049dbcc21b/test/issue53.cpp#L15  
fails on msvc-14.1 https://ci.appveyor.com/project/apolukhin/variant-ykfti/branch/develop/job/mtd52h6mhphj3dv9#L4402  
  
```  
C:\boost-local\boost/thread/detail/move.hpp(52): error C2528: '->': pointer to reference is illegal  
C:\boost-local\boost/type_traits/is_convertible.hpp(490): note: see reference to class template instantiation 'boost::detail::thread_move_t<T>' being compiled  
        with  
        [  
            T=T0 &  
        ]  
C:\boost-local\boost/core/enable_if.hpp(41): note: see reference to class template instantiation 'boost::is_convertible<T &,boost::detail::thread_move_t<spanac&>>' being compiled  
        with  
        [  
            T=T0  
        ]  
C:\boost-local\boost/variant/variant.hpp(1020): note: see reference to class template instantiation 'boost::enable_if<boost::is_convertible<T &,boost::detail::thread_move_t<spanac&>>,boost::detail::thread_move_t<spanac&>>' being compiled  
        with  
        [  
            T=T0  
        ]  
C:\boost-local\boost/variant/detail/visitation_impl.hpp(112): note: see reference to function template instantiation 'bool boost::detail::variant::invoke_visitor<Visitor,true>::internal_visit<T&>(spanac&,int)' being compiled  
        with  
        [  
            Visitor=visitor_t,  
            T=T0  
        ]  
```  
  
Possible fixes:  
* move [the function](https://github.com/boostorg/thread/blob/800d0ca597d9ac05c3bc88e729271189107481ee/include/boost/thread/detail/move.hpp#L64) into a separate namespace `boost::thread::detail`  
* change the triat [for move](https://github.com/boostorg/thread/blob/800d0ca597d9ac05c3bc88e729271189107481ee/include/boost/thread/detail/move.hpp#L64) from is_convertible to something else... for example   
```  
typename enable_if_c<T::use_thread_move, boost::detail::thread_move_t<T> >::type move(T& t)  
    {  
        return boost::detail::thread_move_t<T>(t);  
    }  
```

---

## Comment 1

> Username: viboes  
> Created at: 2019-01-09 21:48:12 UTC  
> Url: https://github.com/boostorg/thread/issues/264#issuecomment-452880742  

IIRC boost::move was there since the versions from Anthony Williams of Boost.Thread.  
We added the BOOST_THREAD_USES_MOVE to make it possible to use the better move sematic emulation from Boost.Move.  
  
Unfortunately BOOST_THREAD_USES_MOVE is not defined by default. You need to define a higher version of Boost.Thread.  
  
#define BOOST_THREAD_VERSION 3  
  
https://godbolt.org/z/3eayCt  
  
If you have a non breaking change PR I will be open to analyze it.
