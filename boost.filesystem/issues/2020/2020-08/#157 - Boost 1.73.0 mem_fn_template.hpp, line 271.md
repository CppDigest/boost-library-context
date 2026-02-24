# #157 - Boost 1.73.0 mem_fn_template.hpp, line 271 [Closed]

> Username: SPlatten  
> Created at: 2020-08-06 10:11:52 UTC  
> Updated at: 2020-08-06 10:25:30 UTC  
> Closed at: 2020-08-06 10:25:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/157  

The following errors are reported when compiling project with Boost 1.73.0:  
  
``1>------ Rebuild All started: Project: Process Control Service Group, Configuration: Debug Win32 ------  
1>Recording build details  
1>"Built: 06/08/2020 11:04:04 (HW-WOP-113835:U49100)"  
1>  
1>entry.cpp  
1>process.cpp  
1>processcontrolengine.cpp  
1>processscope.cpp  
1>c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2672: 'get_pointer': no matching overloaded function found  
1>c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(286): note: see reference to function template instantiation 'R boost::_mfi::mf2<R,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>::call<U,const SystemObjectPtr,A2>(U &,const void *,B1 &,B2 &) const' being compiled  
1>        with  
1>        [  
1>            R=void,  
1>            U=std::_Ph<1>,  
1>            A2=ProcessControl::ProcessChange,  
1>            B1=const SystemObjectPtr,  
1>            B2=ProcessControl::ProcessChange  
1>        ]  
1>c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(286): note: see reference to function template instantiation 'R boost::_mfi::mf2<R,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>::call<U,const SystemObjectPtr,A2>(U &,const void *,B1 &,B2 &) const' being compiled  
1>        with  
1>        [  
1>            R=void,  
1>            U=std::_Ph<1>,  
1>            A2=ProcessControl::ProcessChange,  
1>            B1=const SystemObjectPtr,  
1>            B2=ProcessControl::ProcessChange  
1>        ]  
1>c:\boost\boost_1_73_0\boost\bind\bind.hpp(398): note: see reference to function template instantiation 'R boost::_mfi::mf2<R,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>::operator ()<T>(U &,A1,A2) const' being compiled  
1>        with  
1>        [  
1>            R=void,  
1>            T=std::_Ph<1>,  
1>            U=std::_Ph<1>,  
1>            A1=SystemObjectPtrRef,  
1>            A2=ProcessControl::ProcessChange  
1>        ]  
1>c:\boost\boost_1_73_0\boost\bind\bind.hpp(398): note: see reference to function template instantiation 'R boost::_mfi::mf2<R,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>::operator ()<T>(U &,A1,A2) const' being compiled  
1>        with  
1>        [  
1>            R=void,  
1>            T=std::_Ph<1>,  
1>            U=std::_Ph<1>,  
1>            A1=SystemObjectPtrRef,  
1>            A2=ProcessControl::ProcessChange  
1>        ]  
1>c:\boost\boost_1_73_0\boost\bind\bind.hpp(1306): note: see reference to function template instantiation 'void boost::_bi::list3<boost::_bi::value<T>,boost::_bi::value<boost::shared_ptr<ISystemObject>>,boost::_bi::value<ProcessControl::ProcessChange>>::operator ()<F,boost::_bi::rrlist1<A1>>(boost::_bi::type<void>,F &,A &,int)' being compiled  
1>        with  
1>        [  
1>            T=std::_Ph<1>,  
1>            F=boost::_mfi::mf2<void,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>,  
1>            A1=const boost::intrusive_ptr<IProcessChangeObserver> &,  
1>            A=boost::_bi::rrlist1<const boost::intrusive_ptr<IProcessChangeObserver> &>  
1>        ]  
1>c:\boost\boost_1_73_0\boost\bind\bind.hpp(1306): note: see reference to function template instantiation 'void boost::_bi::list3<boost::_bi::value<T>,boost::_bi::value<boost::shared_ptr<ISystemObject>>,boost::_bi::value<ProcessControl::ProcessChange>>::operator ()<F,boost::_bi::rrlist1<A1>>(boost::_bi::type<void>,F &,A &,int)' being compiled  
1>        with  
1>        [  
1>            T=std::_Ph<1>,  
1>            F=boost::_mfi::mf2<void,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>,  
1>            A1=const boost::intrusive_ptr<IProcessChangeObserver> &,  
1>            A=boost::_bi::rrlist1<const boost::intrusive_ptr<IProcessChangeObserver> &>  
1>        ]  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\algorithm(92): note: see reference to function template instantiation 'void boost::_bi::bind_t<void,boost::_mfi::mf2<void,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>,boost::_bi::list3<boost::_bi::value<T>,boost::_bi::value<boost::shared_ptr<ISystemObject>>,boost::_bi::value<ProcessControl::ProcessChange>>>::operator ()<const boost::intrusive_ptr<IProcessChangeObserver>&>(A1)' being compiled  
1>        with  
1>        [  
1>            T=std::_Ph<1>,  
1>            A1=const boost::intrusive_ptr<IProcessChangeObserver> &  
1>        ]  
1>c:\program files (x86)\microsoft visual studio\2017\enterprise\vc\tools\msvc\14.16.27023\include\algorithm(92): note: see reference to function template instantiation 'void boost::_bi::bind_t<void,boost::_mfi::mf2<void,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>,boost::_bi::list3<boost::_bi::value<T>,boost::_bi::value<boost::shared_ptr<ISystemObject>>,boost::_bi::value<ProcessControl::ProcessChange>>>::operator ()<const boost::intrusive_ptr<IProcessChangeObserver>&>(A1)' being compiled  
1>        with  
1>        [  
1>            T=std::_Ph<1>,  
1>            A1=const boost::intrusive_ptr<IProcessChangeObserver> &  
1>        ]  
1>c:\fraenkelsoftware-millikan\core\service groups\process control\code\processscope.cpp(197): note: see reference to function template instantiation '_Fn std::for_each<std::_List_const_iterator<std::_List_val<std::_List_simple_types<_Ty>>>,boost::_bi::bind_t<void,boost::_mfi::mf2<void,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>,boost::_bi::list3<boost::_bi::value<T>,boost::_bi::value<boost::shared_ptr<ISystemObject>>,boost::_bi::value<ProcessControl::ProcessChange>>>>(_InIt,_InIt,_Fn)' being compiled  
1>        with  
1>        [  
1>            _Fn=boost::_bi::bind_t<void,boost::_mfi::mf2<void,IProcessChangeObserver,SystemObjectPtrRef,ProcessControl::ProcessChange>,boost::_bi::list3<boost::_bi::value<std::_Ph<1>>,boost::_bi::value<SystemObjectPtr>,boost::_bi::value<ProcessControl::ProcessChange>>>,  
1>            _Ty=boost::intrusive_ptr<IProcessChangeObserver>,  
1>            T=std::_Ph<1>,  
1>            _InIt=std::_List_const_iterator<std::_List_val<std::_List_simple_types<boost::intrusive_ptr<IProcessChangeObserver>>>>  
1>        ]  
1>c:\boost\boost_1_73_0\boost\bind\placeholders.hpp(54): note: see reference to class template instantiation 'boost::arg<9>' being compiled  
1>c:\boost\boost_1_73_0\boost\bind\placeholders.hpp(53): note: see reference to class template instantiation 'boost::arg<8>' being compiled  
1>c:\boost\boost_1_73_0\boost\bind\placeholders.hpp(52): note: see reference to class template instantiation 'boost::arg<7>' being compiled  
1>c:\boost\boost_1_73_0\boost\bind\placeholders.hpp(51): note: see reference to class template instantiation 'boost::arg<6>' being compiled  
1>c:\boost\boost_1_73_0\boost\bind\placeholders.hpp(50): note: see reference to class template instantiation 'boost::arg<5>' being compiled  
1>c:\boost\boost_1_73_0\boost\bind\placeholders.hpp(49): note: see reference to class template instantiation 'boost::arg<4>' being compiled  
1>c:\boost\boost_1_73_0\boost\bind\placeholders.hpp(48): note: see reference to class template instantiation 'boost::arg<3>' being compiled  
1>c:\boost\boost_1_73_0\boost\bind\placeholders.hpp(47): note: see reference to class template instantiation 'boost::arg<2>' being compiled  
1>c:\boost\boost_1_73_0\boost\bind\placeholders.hpp(46): note: see reference to class template instantiation 'boost::arg<1>' being compiled  
1>c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2784: 'T *boost::get_pointer(const boost::scoped_ptr<T> &) noexcept': could not deduce template argument for 'const boost::scoped_ptr<T> &' from 'U'  
1>        with  
1>        [  
1>            U=std::_Ph<1>  
1>        ]  
1>c:\boost\boost_1_73_0\boost\smart_ptr\scoped_ptr.hpp(156): note: see declaration of 'boost::get_pointer'  
1>c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2784: 'optional<T>::pointer_type boost::get_pointer(boost::optional<T> &)': could not deduce template argument for 'boost::optional<T> &' from 'U'  
1>        with  
1>        [  
1>            U=std::_Ph<1>  
1>        ]  
1>c:\boost\boost_1_73_0\boost\optional\optional.hpp(1582): note: see declaration of 'boost::get_pointer'  
1>c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2784: 'optional<T>::pointer_const_type boost::get_pointer(const boost::optional<T> &)': could not deduce template argument for 'const boost::optional<T> &' from 'U'  
1>        with  
1>        [  
1>            U=std::_Ph<1>  
1>        ]  
1>c:\boost\boost_1_73_0\boost\optional\optional.hpp(1574): note: see declaration of 'boost::get_pointer'  
1>c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2784: 'T *boost::get_pointer(const std::shared_ptr<_Other> &)': could not deduce template argument for 'const std::shared_ptr<_Other> &' from 'U'  
1>        with  
1>        [  
1>            U=std::_Ph<1>  
1>        ]  
1>c:\boost\boost_1_73_0\boost\get_pointer.hpp(67): note: see declaration of 'boost::get_pointer'  
1>c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2784: 'T *boost::get_pointer(const std::unique_ptr<_Ty,std::default_delete<_Ty>> &)': could not deduce template argument for 'const std::unique_ptr<_Ty,std::default_delete<_Ty>> &' from 'U'  
1>        with  
1>        [  
1>            U=std::_Ph<1>  
1>        ]  
1>c:\boost\boost_1_73_0\boost\get_pointer.hpp(62): note: see declaration of 'boost::get_pointer'  
1>c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2784: 'T *boost::get_pointer(const std::auto_ptr<_Ty> &)': could not deduce template argument for 'const std::auto_ptr<_Ty> &' from 'U'  
1>        with  
1>        [  
1>            U=std::_Ph<1>  
1>        ]  
1>c:\boost\boost_1_73_0\boost\get_pointer.hpp(48): note: see declaration of 'boost::get_pointer'  
1>c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2784: 'T *boost::get_pointer(T *)': could not deduce template argument for 'T *' from 'U'  
1>        with  
1>        [  
1>            U=std::_Ph<1>  
1>        ]  
1>c:\boost\boost_1_73_0\boost\get_pointer.hpp(20): note: see declaration of 'boost::get_pointer'  
1>c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2784: 'T *boost::get_pointer(const boost::intrusive_ptr<T> &) noexcept': could not deduce template argument for 'const boost::intrusive_ptr<T> &' from 'U'  
1>        with  
1>        [  
1>            U=std::_Ph<1>  
1>        ]  
1>c:\boost\boost_1_73_0\boost\smart_ptr\intrusive_ptr.hpp(295): note: see declaration of 'boost::get_pointer'  
1>c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2784: 'shared_ptr<X>::element_type *boost::get_pointer(const boost::shared_ptr<X> &) noexcept': could not deduce template argument for 'const boost::shared_ptr<X> &' from 'U'  
1>        with  
1>        [  
1>            U=std::_Ph<1>  
1>        ]  
1>c:\boost\boost_1_73_0\boost\smart_ptr\shared_ptr.hpp(964): note: see declaration of 'boost::get_pointer'  
1>c:\boost\boost_1_73_0\boost\bind\mem_fn_template.hpp(271): error C2784: 'T *boost::get_pointer(const boost::reference_wrapper<T> &)': could not deduce template argument for 'const boost::reference_wrapper<T> &' from 'U'  
1>        with  
1>        [  
1>            U=std::_Ph<1>  
1>        ]  
1>c:\boost\boost_1_73_0\boost\core\ref.hpp(292): note: see declaration of 'boost::get_pointer'  
1>Generating Code...  
1>Done building project "Process Control Service Group.vcxproj" -- FAILED.  
========== Rebuild All: 0 succeeded, 1 failed, 0 skipped ==========

---

## Comment 1

> Username: Lastique  
> Created at: 2020-08-06 10:25:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/157#issuecomment-669846438  

I don't see how this is related to Boost.Filesystem.  
  
You might want to report it to [Boost.Bind](https://github.com/boostorg/bind/issues), with a reproducer code sample. From what I could gather from the error message, the problem is that you're using a standard library placeholder (e.g. `std::placeholders::_1`) for an object pointer that you're passing to `boost::mem_fn`. First, you should be using Boost.Bind placeholders (e.g. `boost::placeholders::_1`). Second, I don't think passing a placeholder as an object pointer to `boost::mem_fn` is supported.
