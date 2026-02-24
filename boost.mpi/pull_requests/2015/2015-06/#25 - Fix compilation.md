# #25 Fix compilation [Merged]

> Username: MarcelRaad  
> Created at: 2015-06-06 18:47:06 UTC  
> Updated at: 2015-09-21 21:52:42 UTC  
> Merged at: 2015-09-21 21:52:42 UTC  
> Closed at: 2015-09-21 21:52:42 UTC  
> Url: https://github.com/boostorg/mpi/pull/25  

There were several errors when compiling with MSVC 14 and --build-type=complete because of wrong function signatures.

---

## Comment 1

> Username: aminiussi  
> Created_at: 2015-06-11 15:45:12 UTC  
> Url: https://github.com/boostorg/mpi/pull/25#issuecomment-111178218  

Yes, apparently the serialization develop branch was merged onto master which broke mpi build.  
Normaly, the developp branch of mpi should already fix that. Can you confirm if you still have this issue with serialization/master en mpi/develop ?  
(I do not have acess to MSVC)  
  
Regards

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2015-06-11 17:55:54 UTC  
> Url: https://github.com/boostorg/mpi/pull/25#issuecomment-111223008  

I can confirm the issue occurs both with mpi/develop + serialization/develop and mpi/develop + serialization/master. But only with --build-type=complete, which is why it doesn't appear in the develop regression test results, I guess.  
  
The first error I'm getting is:  
  
```  
.\boost/mpi/detail/ignore_skeleton_oarchive.hpp(64): error C2664: 'void boost::mpi::detail::ignore_skeleton_oarchive<boost::mpi::detail::content_oarchive>::save_override(const std::string &,int)': cannot convert argument 1 from 'const boost::serialization::array<const _Elem>' to 'const std::string &'  
        with  
        [  
            _Elem=char  
        ]  
.\boost/mpi/detail/ignore_skeleton_oarchive.hpp(64): note: Reason: cannot convert from 'const boost::serialization::array<const _Elem>' to 'const std::string'  
        with  
        [  
            _Elem=char  
        ]  
.\boost/mpi/detail/ignore_skeleton_oarchive.hpp(64): note: No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
.\boost/mpi/detail/ignore_skeleton_oarchive.hpp(62): note: while compiling class template member function 'void boost::mpi::detail::ignore_skeleton_oarchive<boost::mpi::detail::content_oarchive>::save_override(const std::string &,int)'  
.\boost/mpi/detail/content_oarchive.hpp(29): note: see reference to class template instantiation 'boost::mpi::detail::ignore_skeleton_oarchive<boost::mpi::detail::content_oarchive>' being compiled´  
```  
  
And after fixing that, similar errors appear for the other two files I changed.

---
