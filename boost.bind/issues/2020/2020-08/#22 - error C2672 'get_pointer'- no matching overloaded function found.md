# #22 - error C2672 'get_pointer': no matching overloaded function found [Closed]

> Username: SPlatten  
> Created at: 2020-08-06 10:36:18 UTC  
> Updated at: 2023-08-11 08:57:05 UTC  
> Closed at: 2021-03-06 21:16:10 UTC  
> Url: https://github.com/boostorg/bind/issues/22  

I'm using Boost 1.73 the error is reported in:  
`  
c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2672: 'get_pointer': no matching overloaded function found  
c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(286): note: see reference to function template instantiation 'R boost::_mfi::mf2<R,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>::call<U,const SystemObjectPtr,A2>(U &,const void *,B1 &,B2 &) const' being compiled  
        with  
        [  
            R=void,  
            U=std::_Ph<1>,  
            A2=ProcessControl::ProcessChange,  
            B1=const SystemObjectPtr,  
            B2=ProcessControl::ProcessChange  
        ]  
c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(286): note: see reference to function template instantiation 'R boost::_mfi::mf2<R,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>::call<U,const SystemObjectPtr,A2>(U &,const void *,B1 &,B2 &) const' being compiled  
        with  
        [  
            R=void,  
            U=std::_Ph<1>,  
            A2=ProcessControl::ProcessChange,  
            B1=const SystemObjectPtr,  
            B2=ProcessControl::ProcessChange  
        ]  
c:\boost\boost_1_73_0\boost\bind\bind.hpp(398): note: see reference to function template instantiation 'R boost::_mfi::mf2<R,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>::operator ()<T>(U &,A1,A2) const' being compiled  
        with  
        [  
            R=void,  
            T=std::_Ph<1>,  
            U=std::_Ph<1>,  
            A1=SystemObjectPtrRef,  
            A2=ProcessControl::ProcessChange  
        ]  
c:\boost\boost_1_73_0\boost\bind\bind.hpp(398): note: see reference to function template instantiation 'R boost::_mfi::mf2<R,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>::operator ()<T>(U &,A1,A2) const' being compiled  
        with  
        [  
            R=void,  
            T=std::_Ph<1>,  
            U=std::_Ph<1>,  
            A1=SystemObjectPtrRef,  
            A2=ProcessControl::ProcessChange  
        ]  
c:\boost\boost_1_73_0\boost\bind\bind.hpp(1306): note: see reference to function template instantiation 'void boost::_bi::list3<boost::_bi::value<T>,boost::_bi::value<boost::shared_ptr<ISystemObject>>,boost::_bi::value<ProcessControl::ProcessChange>>::operator ()<F,boost::_bi::rrlist1<A1>>(boost::_bi::type<void>,F &,A &,int)' being compiled  
        with  
        [  
            T=std::_Ph<1>,  
            F=boost::_mfi::mf2<void,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>,  
            A1=const boost::intrusive_ptr<IProcessChangeObserver> &,  
            A=boost::_bi::rrlist1<const boost::intrusive_ptr<IProcessChangeObserver> &>  
        ]  
c:\boost\boost_1_73_0\boost\bind\bind.hpp(1306): note: see reference to function template instantiation 'void boost::_bi::list3<boost::_bi::value<T>,boost::_bi::value<boost::shared_ptr<ISystemObject>>,boost::_bi::value<ProcessControl::ProcessChange>>::operator ()<F,boost::_bi::rrlist1<A1>>(boost::_bi::type<void>,F &,A &,int)' being compiled  
        with  
        [  
            T=std::_Ph<1>,  
            F=boost::_mfi::mf2<void,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>,  
            A1=const boost::intrusive_ptr<IProcessChangeObserver> &,  
            A=boost::_bi::rrlist1<const boost::intrusive_ptr<IProcessChangeObserver> &>  
        ]  
c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\algorithm(92): note: see reference to function template instantiation 'void boost::_bi::bind_t<void,boost::_mfi::mf2<void,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>,boost::_bi::list3<boost::_bi::value<T>,boost::_bi::value<boost::shared_ptr<ISystemObject>>,boost::_bi::value<ProcessControl::ProcessChange>>>::operator ()<const boost::intrusive_ptr<IProcessChangeObserver>&>(A1)' being compiled  
        with  
        [  
            T=std::_Ph<1>,  
            A1=const boost::intrusive_ptr<IProcessChangeObserver> &  
        ]  
c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\algorithm(92): note: see reference to function template instantiation 'void boost::_bi::bind_t<void,boost::_mfi::mf2<void,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>,boost::_bi::list3<boost::_bi::value<T>,boost::_bi::value<boost::shared_ptr<ISystemObject>>,boost::_bi::value<ProcessControl::ProcessChange>>>::operator ()<const boost::intrusive_ptr<IProcessChangeObserver>&>(A1)' being compiled  
        with  
        [  
            T=std::_Ph<1>,  
            A1=const boost::intrusive_ptr<IProcessChangeObserver> &  
        ]  
`

---

## Comment 1

> Username: pdimov  
> Created at: 2020-08-06 11:44:20 UTC  
> Url: https://github.com/boostorg/bind/issues/22#issuecomment-669878224  

What is the code that fails? Is this a regression, that is, did this same code work with an earlier version of Boost?

---

## Comment 2

> Username: SPlatten  
> Created at: 2020-08-06 11:51:55 UTC  
> Url: https://github.com/boostorg/bind/issues/22#issuecomment-669881368  

From a clean build this is the output, hopefully if gives you the information you are after?  
`------ Rebuild All started: Project: Process Control Service Group, Configuration: Debug Win32 ------  
Recording build details  
"Built: 06/08/2020 12:50:17 (HW-WOP-113835:U49100)"  
  
entry.cpp  
process.cpp  
processcontrolengine.cpp  
processscope.cpp  
c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2672: 'get_pointer': no matching overloaded function found  
c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(286): note: see reference to function template instantiation 'R boost::_mfi::mf2<R,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>::call<U,const SystemObjectPtr,A2>(U &,const void *,B1 &,B2 &) const' being compiled  
        with  
        [  
            R=void,  
            U=std::_Ph<,  
            A2=ProcessControl::ProcessChange,  
            B1=const SystemObjectPtr,  
            B2=ProcessControl::ProcessChange  
        ]  
c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(286): note: see reference to function template instantiation 'R boost::_mfi::mf2<R,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>::call<U,const SystemObjectPtr,A2>(U &,const void *,B1 &,B2 &) const' being compiled  
        with  
        [  
            R=void,  
            U=std::_Ph<,  
            A2=ProcessControl::ProcessChange,  
            B1=const SystemObjectPtr,  
            B2=ProcessControl::ProcessChange  
        ]  
c:\boost\boost_1_73_0\boost\bind\bind.hpp(398): note: see reference to function template instantiation 'R boost::_mfi::mf2<R,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>::operator ()<T>(U &,A1,A2) const' being compiled  
        with  
        [  
            R=void,  
            T=std::_Ph<,  
            U=std::_Ph<,  
            A1=SystemObjectPtrRef,  
            A2=ProcessControl::ProcessChange  
        ]  
c:\boost\boost_1_73_0\boost\bind\bind.hpp(398): note: see reference to function template instantiation 'R boost::_mfi::mf2<R,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>::operator ()<T>(U &,A1,A2) const' being compiled  
        with  
        [  
            R=void,  
            T=std::_Ph<,  
            U=std::_Ph<,  
            A1=SystemObjectPtrRef,  
            A2=ProcessControl::ProcessChange  
        ]  
c:\boost\boost_1_73_0\boost\bind\bind.hpp(1306): note: see reference to function template instantiation 'void boost::_bi::list3<boost::_bi::value<T>,boost::_bi::value<boost::shared_ptr<ISystemObject>>,boost::_bi::value<ProcessControl::ProcessChange>>::operator ()<F,boost::_bi::rrlist1<A>(boost::_bi::type<void>,F &,A &,int)' being compiled  
        with  
        [  
            T=std::_Ph<,  
            F=boost::_mfi::mf2<void,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>,  
            A1=const boost::intrusive_ptr<IProcessChangeObserver> &,  
            A=boost::_bi::rrlist1<const boost::intrusive_ptr<IProcessChangeObserver> &>  
        ]  
c:\boost\boost_1_73_0\boost\bind\bind.hpp(1306): note: see reference to function template instantiation 'void boost::_bi::list3<boost::_bi::value<T>,boost::_bi::value<boost::shared_ptr<ISystemObject>>,boost::_bi::value<ProcessControl::ProcessChange>>::operator ()<F,boost::_bi::rrlist1<A>(boost::_bi::type<void>,F &,A &,int)' being compiled  
        with  
        [  
            T=std::_Ph<,  
            F=boost::_mfi::mf2<void,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>,  
            A1=const boost::intrusive_ptr<IProcessChangeObserver> &,  
            A=boost::_bi::rrlist1<const boost::intrusive_ptr<IProcessChangeObserver> &>  
        ]  
c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\algorithm(92): note: see reference to function template instantiation 'void boost::_bi::bind_t<void,boost::_mfi::mf2<void,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>,boost::_bi::list3<boost::_bi::value<T>,boost::_bi::value<boost::shared_ptr<ISystemObject>>,boost::_bi::value<ProcessControl::ProcessChange>>>::operator ()<const boost::intrusive_ptr<IProcessChangeObserver>&>(A1)' being compiled  
        with  
        [  
            T=std::_Ph<,  
            A1=const boost::intrusive_ptr<IProcessChangeObserver> &  
        ]  
c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\algorithm(92): note: see reference to function template instantiation 'void boost::_bi::bind_t<void,boost::_mfi::mf2<void,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>,boost::_bi::list3<boost::_bi::value<T>,boost::_bi::value<boost::shared_ptr<ISystemObject>>,boost::_bi::value<ProcessControl::ProcessChange>>>::operator ()<const boost::intrusive_ptr<IProcessChangeObserver>&>(A1)' being compiled  
        with  
        [  
            T=std::_Ph<,  
            A1=const boost::intrusive_ptr<IProcessChangeObserver> &  
        ]  
c:\fraenkelsoftware-millikan\core\service groups\process control\code\processscope.cpp(197): note: see reference to function template instantiation '_Fn std::for_each<std::_List_const_iterator<std::_List_val<std::_List_simple_types<_Ty>>>,boost::_bi::bind_t<void,boost::_mfi::mf2<void,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>,boost::_bi::list3<boost::_bi::value<T>,boost::_bi::value<boost::shared_ptr<ISystemObject>>,boost::_bi::value<ProcessControl::ProcessChange>>>>(_InIt,_InIt,_Fn)' being compiled  
        with  
        [  
            _Fn=boost::_bi::bind_t<void,boost::_mfi::mf2<void,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>,boost::_bi::list3<boost::_bi::value<std::_Ph<>,boost::_bi::value<SystemObjectPtr>,boost::_bi::value<ProcessControl::ProcessChange>>>,  
            _Ty=boost::intrusive_ptr<IProcessChangeObserver>,  
            T=std::_Ph<,  
            _InIt=std::_List_const_iterator<std::_List_val<std::_List_simple_types<boost::intrusive_ptr<IProcessChangeObserver>>>>  
        ]  
c:\boost\boost_1_73_0\boost\bind\placeholders.hpp(54): note: see reference to class template instantiation 'boost::arg<9>' being compiled  
c:\boost\boost_1_73_0\boost\bind\placeholders.hpp(53): note: see reference to class template instantiation 'boost::arg<8>' being compiled  
c:\boost\boost_1_73_0\boost\bind\placeholders.hpp(52): note: see reference to class template instantiation 'boost::arg<7>' being compiled  
c:\boost\boost_1_73_0\boost\bind\placeholders.hpp(51): note: see reference to class template instantiation 'boost::arg<6>' being compiled  
c:\boost\boost_1_73_0\boost\bind\placeholders.hpp(50): note: see reference to class template instantiation 'boost::arg<5>' being compiled  
c:\boost\boost_1_73_0\boost\bind\placeholders.hpp(49): note: see reference to class template instantiation 'boost::arg<4>' being compiled  
c:\boost\boost_1_73_0\boost\bind\placeholders.hpp(48): note: see reference to class template instantiation 'boost::arg<3>' being compiled  
c:\boost\boost_1_73_0\boost\bind\placeholders.hpp(47): note: see reference to class template instantiation 'boost::arg<2>' being compiled  
c:\boost\boost_1_73_0\boost\bind\placeholders.hpp(46): note: see reference to class template instantiation 'boost::arg<' being compiled  
c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2784: 'T *boost::get_pointer(const boost::scoped_ptr<T> &) noexcept': could not deduce template argument for 'const boost::scoped_ptr<T> &' from 'U'  
        with  
        [  
            U=std::_Ph<  
        ]  
c:\boost\boost_1_73_0\boost\smart_ptr\scoped_ptr.hpp(156): note: see declaration of 'boost::get_pointer'  
c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2784: 'optional<T>::pointer_type boost::get_pointer(boost::optional<T> &)': could not deduce template argument for 'boost::optional<T> &' from 'U'  
        with  
        [  
            U=std::_Ph<  
        ]  
c:\boost\boost_1_73_0\boost\optional\optional.hpp(1582): note: see declaration of 'boost::get_pointer'  
c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2784: 'optional<T>::pointer_const_type boost::get_pointer(const boost::optional<T> &)': could not deduce template argument for 'const boost::optional<T> &' from 'U'  
        with  
        [  
            U=std::_Ph<  
        ]  
c:\boost\boost_1_73_0\boost\optional\optional.hpp(1574): note: see declaration of 'boost::get_pointer'  
c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2784: 'T *boost::get_pointer(const std::shared_ptr<_Other> &)': could not deduce template argument for 'const std::shared_ptr<_Other> &' from 'U'  
        with  
        [  
            U=std::_Ph<  
        ]  
c:\boost\boost_1_73_0\boost\get_pointer.hpp(67): note: see declaration of 'boost::get_pointer'  
c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2784: 'T *boost::get_pointer(const std::unique_ptr<_Ty,std::default_delete<_Ty>> &)': could not deduce template argument for 'const std::unique_ptr<_Ty,std::default_delete<_Ty>> &' from 'U'  
        with  
        [  
            U=std::_Ph<  
        ]  
c:\boost\boost_1_73_0\boost\get_pointer.hpp(62): note: see declaration of 'boost::get_pointer'  
c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2784: 'T *boost::get_pointer(const std::auto_ptr<_Ty> &)': could not deduce template argument for 'const std::auto_ptr<_Ty> &' from 'U'  
        with  
        [  
            U=std::_Ph<  
        ]  
c:\boost\boost_1_73_0\boost\get_pointer.hpp(48): note: see declaration of 'boost::get_pointer'  
c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2784: 'T *boost::get_pointer(T *)': could not deduce template argument for 'T *' from 'U'  
        with  
        [  
            U=std::_Ph<  
        ]  
c:\boost\boost_1_73_0\boost\get_pointer.hpp(20): note: see declaration of 'boost::get_pointer'  
c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2784: 'T *boost::get_pointer(const boost::intrusive_ptr<T> &) noexcept': could not deduce template argument for 'const boost::intrusive_ptr<T> &' from 'U'  
        with  
        [  
            U=std::_Ph<  
        ]  
c:\boost\boost_1_73_0\boost\smart_ptr\intrusive_ptr.hpp(295): note: see declaration of 'boost::get_pointer'  
c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2784: 'shared_ptr<X>::element_type *boost::get_pointer(const boost::shared_ptr<X> &) noexcept': could not deduce template argument for 'const boost::shared_ptr<X> &' from 'U'  
        with  
        [  
            U=std::_Ph<  
        ]  
c:\boost\boost_1_73_0\boost\smart_ptr\shared_ptr.hpp(964): note: see declaration of 'boost::get_pointer'  
c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2784: 'T *boost::get_pointer(const boost::reference_wrapper<T> &)': could not deduce template argument for 'const boost::reference_wrapper<T> &' from 'U'  
        with  
        [  
            U=std::_Ph<  
        ]  
c:\boost\boost_1_73_0\boost\core\ref.hpp(292): note: see declaration of 'boost::get_pointer'  
Generating Code...  
Done building project "Process Control Service Group.vcxproj" -- FAILED.  
========== Rebuild All: 0 succeeded, 1 failed, 0 skipped ==========  
`

---

## Comment 3

> Username: pdimov  
> Created at: 2020-08-06 12:16:23 UTC  
> Url: https://github.com/boostorg/bind/issues/22#issuecomment-669891650  

This doesn't really answer any of my questions, but I can now see what's the reason for the error - the placeholder `_1` refers to the standard `std::placeholders::_1`, rather than to the Boost one, `boost::placeholders::_1`.

---

## Comment 4

> Username: SPlatten  
> Created at: 2020-08-06 12:21:55 UTC  
> Url: https://github.com/boostorg/bind/issues/22#issuecomment-669894058  

Excellent, thank you, no more problem.

---

## Comment 5

> Username: SSStevePye  
> Created at: 2021-02-23 16:56:23 UTC  
> Url: https://github.com/boostorg/bind/issues/22#issuecomment-784348346  

Sorry, hate to tag on to another post but I get a similar error with boost 1.7.5.  Here's my Visual Studio error train:  
  
Rebuild started...  
1>------ Rebuild All started: Project: fem1d, Configuration: Debug Win32 ------  
1>main.cpp  
1>C:\Projects\Insitu\fem1d\1d-fem\fem1d\geolib\ElementHM.ipp(459,25): warning C4018: '<': signed/unsigned mismatch  
1>C:\Projects\Insitu\fem1d\1d-fem\fem1d\geolib\ElementHM.ipp(463,27): warning C4018: '<': signed/unsigned mismatch  
1>C:\Projects\Insitu\fem1d\1d-fem\fem1d\geolib\ElementHM.ipp(472,25): warning C4018: '<': signed/unsigned mismatch  
1>C:\Projects\Insitu\fem1d\1d-fem\fem1d\geolib\ElementHM.ipp(473,27): warning C4018: '<': signed/unsigned mismatch  
1>C:\Projects\Insitu\fem1d\1d-fem\fem1d\geolib\ElementHM.ipp(503,25): warning C4018: '<': signed/unsigned mismatch  
1>C:\Projects\Insitu\fem1d\1d-fem\fem1d\geolib\ElementHM.ipp(504,27): warning C4018: '<': signed/unsigned mismatch  
1>C:\Projects\Insitu\fem1d\1d-fem\fem1d\geolib\ElementHM.ipp(705,25): warning C4018: '<': signed/unsigned mismatch  
1>C:\Projects\Insitu\fem1d\1d-fem\fem1d\geolib\ElementHM.ipp(713,27): warning C4018: '<': signed/unsigned mismatch  
1>C:\Projects\Insitu\fem1d\1d-fem\fem1d\geolib\ElementHM.ipp(750,27): warning C4018: '<': signed/unsigned mismatch  
1>C:\Projects\Insitu\fem1d\1d-fem\fem1d\geolib\ElementHM.ipp(758,27): warning C4018: '<': signed/unsigned mismatch  
1>C:\Projects\Insitu\fem1d\1d-fem\fem1d\geolib\ElementHM.ipp(769,27): warning C4018: '<': signed/unsigned mismatch  
1>C:\Projects\Insitu\fem1d\1d-fem\fem1d\geolib\ElementHM.ipp(1005,29): warning C4018: '<': signed/unsigned mismatch  
1>C:\Projects\Insitu\fem1d\1d-fem\fem1d\geolib\ElementHM.ipp(1013,31): warning C4018: '<': signed/unsigned mismatch  
1>C:\Projects\Insitu\fem1d\1d-fem\fem1d\geolib\ElementHM.ipp(1035,29): warning C4018: '<': signed/unsigned mismatch  
1>C:\local\boost_1_75_0\boost\bind\mem_fn_template.hpp(40,30): error C2672: 'get_pointer': no matching overloaded function found  
1>C:\local\boost_1_75_0\boost\bind\mem_fn_template.hpp(55): message : see reference to function template instantiation 'R boost::_mfi::mf0<R,plasticity::fem::NodeStatic>::call<U>(U &,const void *) const' being compiled  
1>        with  
1>        [  
1>            R=void,  
1>            U=std::_Ph<1>  
1>        ]  
1>C:\local\boost_1_75_0\boost\bind\mem_fn_template.hpp(55): message : see reference to function template instantiation 'R boost::_mfi::mf0<R,plasticity::fem::NodeStatic>::call<U>(U &,const void *) const' being compiled  
1>        with  
1>        [  
1>            R=void,  
1>            U=std::_Ph<1>  
1>        ]  
1>C:\local\boost_1_75_0\boost\bind\bind.hpp(237): message : see reference to function template instantiation 'R boost::_mfi::mf0<R,plasticity::fem::NodeStatic>::operator ()<T>(U &) const' being compiled  
1>        with  
1>        [  
1>            R=void,  
1>            T=std::_Ph<1>,  
1>            U=std::_Ph<1>  
1>        ]  
1>C:\local\boost_1_75_0\boost\bind\bind.hpp(237): message : see reference to function template instantiation 'R boost::_mfi::mf0<R,plasticity::fem::NodeStatic>::operator ()<T>(U &) const' being compiled  
1>        with  
1>        [  
1>            R=void,  
1>            T=std::_Ph<1>,  
1>            U=std::_Ph<1>  
1>        ]  
1>C:\local\boost_1_75_0\boost\bind\bind.hpp(1284): message : see reference to function template instantiation 'void boost::_bi::list1<boost::_bi::value<T>>::operator ()<F,boost::_bi::rrlist1<A1>>(boost::_bi::type<void>,F &,A &,int)' being compiled  
1>        with  
1>        [  
1>            T=std::_Ph<1>,  
1>            F=boost::_mfi::mf0<void,plasticity::fem::NodeStatic>,  
1>            A1=plasticity::fem::NodeStatic *&,  
1>            A=boost::_bi::rrlist1<plasticity::fem::NodeStatic *&>  
1>        ]  
1>C:\local\boost_1_75_0\boost\bind\bind.hpp(1284): message : see reference to function template instantiation 'void boost::_bi::list1<boost::_bi::value<T>>::operator ()<F,boost::_bi::rrlist1<A1>>(boost::_bi::type<void>,F &,A &,int)' being compiled  
1>        with  
1>        [  
1>            T=std::_Ph<1>,  
1>            F=boost::_mfi::mf0<void,plasticity::fem::NodeStatic>,  
1>            A1=plasticity::fem::NodeStatic *&,  
1>            A=boost::_bi::rrlist1<plasticity::fem::NodeStatic *&>  
1>        ]  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.28.29333\include\algorithm(308): message : see reference to function template instantiation 'void boost::_bi::bind_t<void,boost::_mfi::mf0<void,plasticity::fem::NodeStatic>,boost::_bi::list1<boost::_bi::value<T>>>::operator ()<plasticity::fem::NodeStatic*&>(A1)' being compiled  
1>        with  
1>        [  
1>            T=std::_Ph<1>,  
1>            A1=plasticity::fem::NodeStatic *&  
1>        ]  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.28.29333\include\algorithm(308): message : see reference to function template instantiation 'void boost::_bi::bind_t<void,boost::_mfi::mf0<void,plasticity::fem::NodeStatic>,boost::_bi::list1<boost::_bi::value<T>>>::operator ()<plasticity::fem::NodeStatic*&>(A1)' being compiled  
1>        with  
1>        [  
1>            T=std::_Ph<1>,  
1>            A1=plasticity::fem::NodeStatic *&  
1>        ]  
1>C:\Projects\Insitu\fem1d\1d-fem\fem1d\geolib\MeshHM.ipp(130): message : see reference to function template instantiation '_Fn std::for_each<std::_Vector_iterator<std::_Vector_val<std::_Simple_types<_Ty>>>,OP>(_InIt,_InIt,_Fn)' being compiled  
1>        with  
1>        [  
1>            _Fn=boost::_bi::bind_t<void,boost::_mfi::mf0<void,plasticity::fem::NodeStatic>,boost::_bi::list1<boost::_bi::value<std::_Ph<1>>>>,  
1>            _Ty=corlib::MeshHM::NodePtr_,  
1>            OP=boost::_bi::bind_t<void,boost::_mfi::mf0<void,plasticity::fem::NodeStatic>,boost::_bi::list1<boost::_bi::value<std::_Ph<1>>>>,  
1>            _InIt=std::_Vector_iterator<std::_Vector_val<std::_Simple_types<corlib::MeshHM::NodePtr_ >>>  
1>        ]  
1>C:\Projects\Insitu\fem1d\1d-fem\fem1d\main.cpp(409): message : see reference to function template instantiation 'OP corlib::MeshHM::iterateOverNodes<boost::_bi::bind_t<void,boost::_mfi::mf0<void,plasticity::fem::NodeStatic>,boost::_bi::list1<boost::_bi::value<T>>>>(OP)' being compiled  
1>        with  
1>        [  
1>            OP=boost::_bi::bind_t<void,boost::_mfi::mf0<void,plasticity::fem::NodeStatic>,boost::_bi::list1<boost::_bi::value<std::_Ph<1>>>>,  
1>            T=std::_Ph<1>  
1>        ]  
1>C:\local\boost_1_75_0\boost\bind\mem_fn_template.hpp(40,1): error C2784: 'T *boost::get_pointer(const std::shared_ptr<_Ty> &)': could not deduce template argument for 'const std::shared_ptr<_Ty> &' from 'U'  
1>        with  
1>        [  
1>            U=std::_Ph<1>  
1>        ]  
1>C:\local\boost_1_75_0\boost\get_pointer.hpp(67): message : see declaration of 'boost::get_pointer'  
1>C:\local\boost_1_75_0\boost\bind\mem_fn_template.hpp(40,1): error C2784: 'T *boost::get_pointer(const std::unique_ptr<_Ty,std::default_delete<_Ty>> &)': could not deduce template argument for 'const std::unique_ptr<_Ty,std::default_delete<_Ty>> &' from 'U'  
1>        with  
1>        [  
1>            U=std::_Ph<1>  
1>        ]  
1>C:\local\boost_1_75_0\boost\get_pointer.hpp(62): message : see declaration of 'boost::get_pointer'  
1>C:\local\boost_1_75_0\boost\bind\mem_fn_template.hpp(40,1): error C2784: 'T *boost::get_pointer(const std::auto_ptr<_Ty> &)': could not deduce template argument for 'const std::auto_ptr<_Ty> &' from 'U'  
1>        with  
1>        [  
1>            U=std::_Ph<1>  
1>        ]  
1>C:\local\boost_1_75_0\boost\get_pointer.hpp(48): message : see declaration of 'boost::get_pointer'  
1>C:\local\boost_1_75_0\boost\bind\mem_fn_template.hpp(40,1): error C2784: 'T *boost::get_pointer(T *)': could not deduce template argument for 'T *' from 'U'  
1>        with  
1>        [  
1>            U=std::_Ph<1>  
1>        ]  
1>C:\local\boost_1_75_0\boost\get_pointer.hpp(20): message : see declaration of 'boost::get_pointer'  
1>C:\local\boost_1_75_0\boost\bind\mem_fn_template.hpp(40,1): error C2784: 'T *boost::get_pointer(const boost::reference_wrapper<T> &)': could not deduce template argument for 'const boost::reference_wrapper<T> &' from 'U'  
1>        with  
1>        [  
1>            U=std::_Ph<1>  
1>        ]  
1>C:\local\boost_1_75_0\boost\core\ref.hpp(292): message : see declaration of 'boost::get_pointer'  
1>Done building project "fem1d.vcxproj" -- FAILED.  
========== Rebuild All: 0 succeeded, 1 failed, 0 skipped ==========

---

## Comment 6

> Username: pdimov  
> Created at: 2021-02-23 17:50:35 UTC  
> Url: https://github.com/boostorg/bind/issues/22#issuecomment-784385225  

The cause of the error is the same: you're passing the standard _1 from namespace std::placeholders to boost::bind.  
```  
1>            _Fn=boost::_bi::bind_t<void,boost::_mfi::mf0<void,plasticity::fem::NodeStatic>,boost::_bi::list1<boost::_bi::value<std::_Ph<1>>>>,  
```  
I suppose I need to just make this work.

---

## Comment 7

> Username: SSStevePye  
> Created at: 2021-02-23 20:50:09 UTC  
> Url: https://github.com/boostorg/bind/issues/22#issuecomment-784502303  

Thank you.  I was trying to build some software we got from a 3rd party. I  
got past the compile errors only to get into boost library linking issues.  
There's some magic happening where Visual Studio knows the names of the  
boost libraries (including boost version) without me telling it.  I think I  
will throw the project back over the wall and concede defeat.  
   
  _____    
  
From: Peter Dimov [mailto:notifications@github.com]   
Sent: February 23, 2021 10:51 AM  
To: boostorg/bind  
Cc: SSStevePye; Comment  
Subject: Re: [boostorg/bind] error C2672 'get_pointer': no matching  
overloaded function found (#22)  
  
  
  
The cause of the error is the same: you're passing the standard _1 from  
namespace std::placeholders to boost::bind.  
  
1>  
_Fn=boost::_bi::bind_t<void,boost::_mfi::mf0<void,plasticity::fem::NodeStati  
c>,boost::_bi::list1<boost::_bi::value<std::_Ph<1>>>>,  
  
  
  
I suppose I need to just make this work.  
  
-  
You are receiving this because you commented.  
Reply to this email directly, view it  
<https://github.com/boostorg/bind/issues/22#issuecomment-784385225> on  
GitHub, or unsubscribe  
<https://github.com/notifications/unsubscribe-auth/AGLVYHYCZP6WLJFGWD5YIR3TA  
PTHVANCNFSM4PWOHLXQ> .  
<https://github.com/notifications/beacon/AGLVYH2R7EBXZYDZXVOVRRLTAPTHVA5CNFS  
M4PWOHLX2YY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOF3AMJSI  
.gif>

---

## Comment 8

> Username: pdimov  
> Created at: 2021-02-25 08:06:25 UTC  
> Updated at: 2021-02-25 08:08:12 UTC  
> Url: https://github.com/boostorg/bind/issues/22#issuecomment-785703269  

The autolink magic is documented here: https://www.boost.org/doc/libs/1_75_0/libs/config/doc/html/boost_config/boost_macro_reference.html#boost_config.boost_macro_reference.macros_for_libraries_with_separate_source_code.automatic_library_selection  
  
In particular, defining BOOST_ALL_NO_LIB disables it.

---

## Comment 9

> Username: pdimov  
> Created at: 2021-03-06 21:16:10 UTC  
> Url: https://github.com/boostorg/bind/issues/22#issuecomment-792055955  

Added support for using the standard placeholders in https://github.com/boostorg/bind/commit/c85b31e3d200dda2a73cf0027a82c6d8e29066f8.

---

## Comment 10

> Username: Jason-Lee0  
> Created at: 2023-08-11 08:03:45 UTC  
> Url: https://github.com/boostorg/bind/issues/22#issuecomment-1674356376  

Sorry to bother you.  @pdimov   
  
I encountered the same issue. In the past comment, you said can pass the standard _1 from namespace std::placeholders to boost::bind.  
  
Could you tell me where could I change Define namespace ?   
  
I tried to declare the namespace in bind.hpp, but it still showed the error.  
  
Thanks for your reading.  
  
----------------------------  
OS : Windows 10  
Boost versoin : 1.6.6

---

## Comment 11

> Username: pdimov  
> Created at: 2023-08-11 08:57:05 UTC  
> Url: https://github.com/boostorg/bind/issues/22#issuecomment-1674416877  

What code are you trying to compile, and what are the errors you are getting?
