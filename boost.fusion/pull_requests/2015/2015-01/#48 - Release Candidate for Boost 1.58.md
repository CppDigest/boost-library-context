# #48 Release Candidate for Boost 1.58 [Merged]

> Username: Flast  
> Created at: 2015-01-16 07:23:38 UTC  
> Updated at: 2015-02-08 22:55:09 UTC  
> Merged at: 2015-02-08 22:55:09 UTC  
> Closed at: 2015-02-08 22:55:09 UTC  
> Url: https://github.com/boostorg/fusion/pull/48  

I think that develop branch is now stable and good for next release, don't you?

---

## Comment 1

> Username: djowel  
> Created_at: 2015-01-16 07:25:16 UTC  
> Url: https://github.com/boostorg/fusion/pull/48#issuecomment-70217119  

There's still some problems with MSVC. Could you check on VS2013?

---

## Comment 2

> Username: Flast  
> Created_at: 2015-01-16 07:46:09 UTC  
> Url: https://github.com/boostorg/fusion/pull/48#issuecomment-70218601  

[as_deque](http://www.boost.org/development/tests/develop/developer/output/teeks99-08e-win2012R2-64on64-boost-bin-v2-libs-fusion-test-map_move-test-msvc-12-0-debug-threading-multi.html) and [map_move](http://www.boost.org/development/tests/develop/developer/output/teeks99-08e-win2012R2-64on64-boost-bin-v2-libs-fusion-test-map_move-test-msvc-12-0-debug-threading-multi.html) right?   
OK, I'll fix it.

---

## Comment 3

> Username: daminetreg  
> Created_at: 2015-01-16 07:55:11 UTC  
> Url: https://github.com/boostorg/fusion/pull/48#issuecomment-70219396  

I've not been able to finish my work for MSVC for the fusion_adapters. How much time do we have until 1.58 freeze of master ?

---

## Comment 4

> Username: Flast  
> Created_at: 2015-01-16 07:58:19 UTC  
> Url: https://github.com/boostorg/fusion/pull/48#issuecomment-70219622  

There is no announcement about that currently but [wiki](https://svn.boost.org/trac/boost/wiki/ReleaseSchedule) says `First Monday in February`.

---

## Comment 5

> Username: daminetreg  
> Created_at: 2015-01-16 08:31:38 UTC  
> Url: https://github.com/boostorg/fusion/pull/48#issuecomment-70222440  

```  
                                                                                  Thanks, I'll try to check the compatibility with msvc this sunday, possibly I'll also try to fix the other issues noticed by @djowel by compiling develop with it.                                                                                                                                     Cheers,                                                                                                                                                                                                   --Damien Buhl                                                                                                                                                                                                                From: Kohei TakahashiSent: vendredi 16 janvier 2015 08:58To: boostorg/fusionReply To: boostorg/fusionCc: Damien BuhlSubject: Re: [fusion] Release Candidate for Boost 1.58 (#48)There is no announcement about that currently but wiki says First Monday in February.  
```  
  
—Reply to this email directly or view it on GitHub.

---

## Comment 6

> Username: daminetreg  
> Created_at: 2015-01-19 13:17:14 UTC  
> Url: https://github.com/boostorg/fusion/pull/48#issuecomment-70490593  

My PC is dead last week, I spent sunday in restoring it's state. The installer of Visual Studio is almost done, I'll report tomorrow evening (Tuesday) what I could do regarding building fusion with fusion_adapters with MSVC.   
  
@Flast Did you already work on as_deque and map_move ? Following where I stand I could help about one or both tomorrow evening.

---

## Comment 7

> Username: Flast  
> Created_at: 2015-01-20 04:10:20 UTC  
> Url: https://github.com/boostorg/fusion/pull/48#issuecomment-70601914  

> @Flast Did you already work on as_deque and map_move ? Following where I stand I could help about one or both tomorrow evening.  
  
I'm working on [branch bugfix/msvc on my repo](https://github.com/Flast/fusion/compare/boostorg:develop...Flast:bugfix/msvc): will fix `as_deque` and `map_move` partially (some copies are still issued). I've tested under MSVC-12(2013 u4) only, and so test results of some other versions are welcome.

---

## Comment 8

> Username: daminetreg  
> Created_at: 2015-01-21 09:06:37 UTC  
> Url: https://github.com/boostorg/fusion/pull/48#issuecomment-70805063  

I worked yesterday evening on the fusion_adapters for MSVC, it looks like I've got crazy in adding typename everywhere where it wasn't needed. I fixed some of the test, but I still have some errors where ADAPT_STRUCT_TPL. I probably need to differentiate wheter I have a templated type or a template specialization to handle the fact that MSVC doesn't like typename keyword in specialization.  
  
I did not get time to work on map_move or as_deque, sorry for this, I'll inform you as soon as I've fixed the fusion_adapters and that I could help you in testing on other msvc versions.

---

## Comment 9

> Username: Flast  
> Created_at: 2015-01-28 14:28:39 UTC  
> Url: https://github.com/boostorg/fusion/pull/48#issuecomment-71842672  

Boost release schedule was changed: http://lists.boost.org/boost-announce/2015/01/0429.php . Maybe next release date is April 8.

---

## Comment 10

> Username: daminetreg  
> Created_at: 2015-02-02 05:51:18 UTC  
> Url: https://github.com/boostorg/fusion/pull/48#issuecomment-72409408  

Great that it changed a bit, as I'm only done since friday, but couldn't get the time to come here and do the pull-request. My branch fusion_adapters have no test failure on gcc or msvc up to the fix you did @Flast, I will try to merge them to see if it has an impact on my changes, but I don't think so. Then I'll open a pull-request.

---

## Comment 11

> Username: daminetreg  
> Created_at: 2015-02-02 05:52:01 UTC  
> Url: https://github.com/boostorg/fusion/pull/48#issuecomment-72409448  

I meant the branch in my fork naturally. :smile:

---

## Comment 12

> Username: daminetreg  
> Created_at: 2015-02-02 08:09:11 UTC  
> Url: https://github.com/boostorg/fusion/pull/48#issuecomment-72419403  

@Flast I tried also your fixes(#49) with my changes (#50), and all works up to the known std_tuple_iterator and as_deque problems.  
  
How could we share work ? I can work tomorrow evening on the problems on one of them, maybe you already started on something ?

---

## Comment 13

> Username: Flast  
> Created_at: 2015-02-02 09:50:47 UTC  
> Url: https://github.com/boostorg/fusion/pull/48#issuecomment-72429995  

@daminetreg   
  
> all works up to the known std_tuple_iterator and as_deque problems.  
  
Does MSVC show error about `as_deque` with my patch(#49)? I believe it should work and remaining problems are [size / msvc-10.0](http://www.boost.org/development/tests/develop/developer/output/teeks99-08c-win2012R2-64on64-boost-bin-v2-libs-fusion-test-size-test-msvc-10-0-debug-threading-multi.html) and [std_tuple_iterator / msvc-12.0](http://www.boost.org/development/tests/develop/developer/output/teeks99-08e-win2012R2-64on64-boost-bin-v2-libs-fusion-test-std_tuple_iterator-test-msvc-12-0-debug-threading-multi.html).  
  
Regarding `std_tuple_iterator`, it's not only MSVC probrem but also [gcc 4.4](http://www.boost.org/development/tests/develop/developer/output/Sandia-gcc-4-4-7-c++0x-boost-bin-v2-libs-fusion-test-std_tuple_iterator-test-gcc-4-4-7-debug.html), due to non-SFINAE friendly &lt;iterator&gt; ([LWG 2408](http://cplusplus.github.io/LWG/lwg-defects.html#2408)). Thus, I have no way to resolve that problem. (Of course, qualified lookup, like `fusion::next`, should compile as intended).  
FYI: The issue adopted for C++17 but [libstdc++ accepts it within C++11 mode](https://gcc.gnu.org/ml/gcc-patches/2014-11/msg01105.html).  
  
In `size`, it can be fixed by using `mpl::vector<mpl::int_<0>, ...>` instead of C++14 like `integer_sequence` because MSVC-10.0 doesn't compile variadic non-type templates.  
I think it is not hard to fix and I'll do.

---

## Comment 14

> Username: daminetreg  
> Created_at: 2015-02-02 10:10:18 UTC  
> Url: https://github.com/boostorg/fusion/pull/48#issuecomment-72432328  

Thanks for your fast reply. I retested it, I merged your changes on top of my branch fusion_adapters, perhaps it is related to my branch, but I think we are not working in the same area, as I only have preprocessor macro changes.  
  
And when I let it run under MSVC I get 2 target failing, the one with std_tuple_iterator and the one with as_deque, but size do work. Here the compile-output, after I did :   
  
``` sh  
b2 headers  
cd libs  
cd fusion  
cd test  
bjam -a  
```  
  
```  
compile-c-c++ ..\..\..\bin.v2\libs\fusion\test\as_deque.test\msvc-12.0\debug\threading-multi\sequence\as_deque.obj  
as_deque.cpp  
E:\boost_msvc\boost\boost/fusion/container/deque/detail/keyed_element.hpp(93) : warning C4172: returning address of local variable or temporary  
        E:\boost_msvc\boost\boost/fusion/container/deque/detail/keyed_element.hpp(92) : while compiling class template member function 'boost::fusion::detail::nil_keyed_element &&boost::fusion::detail::keyed_element<N,T0,boost::fusion::detail::nil_keyed_element>::forward_base(void)'  
        with  
        [  
            N=boost::mpl::int_<1>  
,            T0=char  
        ]  
        E:\boost_msvc\boost\boost/fusion/container/deque/detail/keyed_element.hpp(64) : see reference to function template instantiation 'boost::fusion::detail::nil_keyed_element &&boost::fusion::detail::keyed_element<N,T0,boost::fusion::detail::nil_keyed_element>::forward_base(void)' being compiled  
        with  
        [  
            N=boost::mpl::int_<1>  
,            T0=char  
        ]  
        E:\boost_msvc\boost\boost/fusion/container/deque/detail/keyed_element.hpp(40) : see reference to class template instantiation 'boost::fusion::detail::keyed_element<N,T0,boost::fusion::detail::nil_keyed_element>' being compiled  
        with  
        [  
            N=boost::mpl::int_<1>  
,            T0=char  
        ]  
        E:\boost_msvc\boost\boost/fusion/container/deque/detail/cpp03/preprocessed/deque10.hpp(14) : see reference to class template instantiation 'boost::fusion::detail::keyed_element<N,T0,boost::fusion::detail::keyed_element<boost::mpl::int_<1>,char,boost::fusion::detail::nil_keyed_element>>' being compiled  
        with  
        [  
            N=boost::mpl::int_<0>  
,            T0=int  
        ]  
        sequence\as_deque.cpp(26) : see reference to class template instantiation 'boost::fusion::deque<int,char,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_>' being compiled  
E:\boost_msvc\boost\boost/fusion/container/deque/detail/cpp03/preprocessed/deque10.hpp(239) : error C2664: 'boost::fusion::detail::keyed_element<N,T0,boost::fusion::detail::nil_keyed_element>::keyed_element(void)' : cannot convert argument 1 from 'boost::fusion::deque<int,char,float,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_>' to 'const boost::fusion::detail::keyed_element<N,T0,boost::fusion::detail::nil_keyed_element> &'  
        with  
        [  
            N=boost::mpl::int_<0>  
,            T0=boost::fusion::deque<int,char,float,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_>  
        ]  
        Reason: cannot convert from 'boost::fusion::deque<int,char,float,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_>' to 'const boost::fusion::detail::keyed_element<N,T0,boost::fusion::detail::nil_keyed_element>'  
        with  
        [  
            N=boost::mpl::int_<0>  
,            T0=boost::fusion::deque<int,char,float,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_>  
        ]  
        No user-defined-conversion operator available that can perform this conversion, or the operator cannot be called  
        E:\boost_msvc\boost\boost/fusion/container/deque/detail/cpp03/preprocessed/as_deque10.hpp(30) : see reference to function template instantiation 'boost::fusion::deque<boost::fusion::deque<int,char,float,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_>,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_>::deque<int,char,float,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_>(boost::fusion::deque<int,char,float,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_> &&)' being compiled  
        E:\boost_msvc\boost\boost/fusion/container/deque/detail/cpp03/preprocessed/as_deque10.hpp(30) : see reference to function template instantiation 'boost::fusion::deque<boost::fusion::deque<int,char,float,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_>,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_>::deque<int,char,float,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_>(boost::fusion::deque<int,char,float,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_> &&)' being compiled  
        E:\boost_msvc\boost\boost/fusion/container/deque/detail/cpp03/build_deque.hpp(50) : see reference to function template instantiation 'boost::fusion::deque<boost::fusion::deque<int,char,float,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_>,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_> boost::fusion::detail::barrier::as_deque<1>::call<boost::fusion::joint_view_iterator<boost::fusion::forward_traversal_tag,boost::fusion::single_view_iterator<Sequence,boost::mpl::int_<0>>,boost::fusion::single_view_iterator<Sequence,boost::mpl::int_<1>>,boost::fusion::deque_iterator<const boost::fusion::deque<boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_>,0>>>(const Iterator &)' being compiled  
        with  
        [  
            Sequence=const boost::fusion::single_view<boost::fusion::deque<int,char,float,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_>>  
,            Iterator=boost::fusion::joint_view_iterator<boost::fusion::forward_traversal_tag,boost::fusion::single_view_iterator<const boost::fusion::single_view<boost::fusion::deque<int,char,float,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_>>,boost::mpl::int_<0>>,boost::fusion::single_view_iterator<const boost::fusion::single_view<boost::fusion::deque<int,char,float,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_>>,boost::mpl::int_<1>>,boost::fusion::deque_iterator<const boost::fusion::deque<boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_>,0>>  
        ]  
        sequence\as_deque.cpp(54) : see reference to function template instantiation 'boost::fusion::deque<boost::fusion::deque<int,char,float,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_>,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_> boost::fusion::as_deque<boost::fusion::joint_view<const boost::fusion::single_view<boost::fusion::deque<int,char,float,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_>>,Sequence>>(const boost::fusion::joint_view<const boost::fusion::single_view<boost::fusion::deque<int,char,float,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_>>,Sequence> &)' being compiled  
        with  
        [  
            Sequence=const boost::fusion::deque<boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_,boost::fusion::void_>  
        ]  
  
    call "C:\Users\Buhl\AppData\Local\Temp\b2_msvc_12.0_vcvarsall_x86.cmd" >nul  
cl /Zm800 -nologo @"..\..\..\bin.v2\libs\fusion\test\as_deque.test\msvc-12.0\debug\threading-multi\sequence\as_deque.obj.rsp"  
  
...failed compile-c-c++ ..\..\..\bin.v2\libs\fusion\test\as_deque.test\msvc-12.0\debug\threading-multi\sequence\as_deque.obj...  
...skipped <p..\..\..\bin.v2\libs\fusion\test\as_deque.test\msvc-12.0\debug\threading-multi>as_deque.exe for lack of <p..\..\..\bin.v2\libs\fusion\test\as_deque.test\msvc-12.0\debug\threading-multi>sequence\as_deque.obj...  
...skipped <p..\..\..\bin.v2\libs\fusion\test\as_deque.test\msvc-12.0\debug\threading-multi>as_deque.run for lack of <p..\..\..\bin.v2\libs\fusion\test\as_deque.test\msvc-12.0\debug\threading-multi>as_deque.exe...  
```

---

## Comment 15

> Username: Flast  
> Created_at: 2015-02-02 10:17:33 UTC  
> Url: https://github.com/boostorg/fusion/pull/48#issuecomment-72433204  

>    b2 headers  
>     cd libs  
>     cd fusion  
>     cd test  
>     bjam -a  
  
Ah, you need to re-generate preprocessed file, like:  
  
```  
cd libs\fusion\preprocess  
bjam  
```

---

## Comment 16

> Username: daminetreg  
> Created_at: 2015-02-02 11:28:52 UTC  
> Url: https://github.com/boostorg/fusion/pull/48#issuecomment-72442270  

Oh sorry, my bad.  
  
Great that we discuss about this, I never did this step in all of my testing. =/ Now I retried and all test passes with my changes + your changes until the std_tuple_iterator one. So as_deque works fine and all other tests runs fine.  
  
So if you are working on it, I'll move to other tasks regarding new features for the ADAPT stuffs.   
  
If you need some support (testing or anything, just tell I would enjoy helping you).

---

## Comment 17

> Username: Flast  
> Created_at: 2015-02-02 14:09:26 UTC  
> Url: https://github.com/boostorg/fusion/pull/48#issuecomment-72462964  

> Now I retried and all test passes with my changes + your changes until the std_tuple_iterator one. So as_deque works fine and all other tests runs fine.  
  
Thank you for testing.  
  
> because MSVC-10.0 doesn't compile variadic non-type templates.  
  
I misunderstood about MSVC supporting: to begin with MSVC-10/11 don't compile variadic templates. I already pushed a patch to #49 for fixing `size`.

---

## Comment 18

> Username: Flast  
> Created_at: 2015-02-05 14:08:55 UTC  
> Url: https://github.com/boostorg/fusion/pull/48#issuecomment-73051103  

> Regarding std_tuple_iterator, it's not only MSVC probrem but also gcc 4.4, due to non-SFINAE friendly <iterator> (LWG 2408). Thus, I have no way to resolve that problem. (Of course, qualified lookup, like fusion::next, should compile as intended).  
> FYI: The issue adopted for C++17 but libstdc++ accepts it within C++11 mode.  
  
I realize how to fix this issue, and open a pull request (#52).

---
