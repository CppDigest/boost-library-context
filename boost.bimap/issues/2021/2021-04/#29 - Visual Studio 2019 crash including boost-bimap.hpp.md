# #29 - Visual Studio 2019 crash including boost/bimap.hpp [Open]

> Username: dgplayer  
> Created at: 2021-04-13 21:33:35 UTC  
> Updated at: 2021-04-14 16:41:41 UTC  
> Url: https://github.com/boostorg/bimap/issues/29  

`  
simply #include <boost/bimap.hpp> crashes the Visual Studio 2019 compiler.  
crash occurs when stdafx.h includes boost/bimap.hpp  
  
boost_1_75_0  
  
C/C++ project properties:  
  
Disable language extensions: no  
Conformance mode: Defailt  
Wchar_t as Built-In: yes  
Force loop scope: yes  
enforce type conversion: not set  
RTTI: not set  
Open MP: not set  
C++ Language Standard ISO C++ 14  
C Language Standard: Default (Legacy MSVC)  
Enaable experimental: not set  
  
1>stdafx.cpp  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\operators.hpp(161,1): fatal error C1001: Internal compiler error.  
1>(compiler file 'msc1.cpp', line 1588)  
1> To work around this problem, try simplifying or changing the program near the locations listed above.  
1>If possible please provide a repro here: https://developercommunity.visualstudio.com  
1>Please choose the Technical Support command on the Visual C++  
1> Help menu, or open the Technical Support help file for more information  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\operators.hpp(141): message : see reference to class template instantiation 'boost::operators_impl::equality_comparable2<T,U,B>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type,  
1>            U=unsigned int,  
1>            B=boost::operators_impl::operators_detail::empty_base<boost::serialization::version_type>  
1>        ]  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\operators.hpp(421): message : see reference to class template instantiation 'boost::operators_impl::less_than_comparable2<T,U,boost::operators_impl::equality_comparable2<T,U,B>>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type,  
1>            U=unsigned int,  
1>            B=boost::operators_impl::operators_detail::empty_base<boost::serialization::version_type>  
1>        ]  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\operators.hpp(168): message : see reference to class template instantiation 'boost::operators_impl::totally_ordered2<boost::serialization::version_type,unsigned int,boost::operators_impl::operators_detail::empty_base<T>>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type  
1>        ]  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\operators.hpp(152): message : see reference to class template instantiation 'boost::operators_impl::equality_comparable1<T,B>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type,  
1>            B=boost::operators_impl::totally_ordered2<boost::serialization::version_type,unsigned int,boost::operators_impl::operators_detail::empty_base<boost::serialization::version_type>>  
1>        ]  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\operators.hpp(427): message : see reference to class template instantiation 'boost::operators_impl::less_than_comparable1<T,boost::operators_impl::equality_comparable1<T,B>>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type,  
1>            B=boost::operators_impl::totally_ordered2<boost::serialization::version_type,unsigned int,boost::operators_impl::operators_detail::empty_base<boost::serialization::version_type>>  
1>        ]  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\serialization\serialization.hpp(52): message : see reference to class template instantiation 'boost::operators_impl::totally_ordered1<boost::serialization::version_type,boost::operators_impl::totally_ordered2<boost::serialization::version_type,unsigned int,boost::operators_impl::operators_detail::empty_base<T>>>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type  
1>        ]  
1>INTERNAL COMPILER ERROR in 'C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\CL.exe'  
1>    Please choose the Technical Support command on the Visual C++  
1>    Help menu, or open the Technical Support help file for more information  
`

---

## Comment 1

> Username: JonCavesMSFT  
> Created at: 2021-04-14 00:44:49 UTC  
> Url: https://github.com/boostorg/bimap/issues/29#issuecomment-819142005  

Hi: some questions: which 2019 compiler? Using the latest build, the just about to be released 16.10 toolset, the most recent 16.9 and 16.8 toolsets I am not seeing any crash. Also the exact command-line would also be helpful.  
  
Thanks

---

## Comment 2

> Username: dgplayer  
> Created at: 2021-04-14 02:09:28 UTC  
> Url: https://github.com/boostorg/bimap/issues/29#issuecomment-819169722  

I just updated to 16.9.4. I still get the crash.  
  
Boost is the first header included in stdafx.h  
  
This compile is for stdafx.cpp which has 1 line that includes stdafx.h.  
  
// stdafx.h : include file for standard system include files,  
// or project specific include files that are used frequently, but  
// are changed infrequently  
//  
#pragma once  
  
  
#include "targetver.h"  
#include "Global/global.h"  
#include "Global/globalext_unix.h"  
  
#include <boost/bimap.hpp>  
#include <string>  
#include <set>  
#include <map>  
  
  
Build started...  
1>------ Build started: Project: ADInf, Configuration: Debug x64 ------  
1>cl : command line warning D9035: option 'Gm' has been deprecated and will be removed in a future release  
1>stdafx.cpp  
1>Note: including file: H:\Svn\branches\MainBuild_2019\Common\Utility\ADInf\stdafx.h  
1>Note: including file: H:\Svn\branches\MainBuild_2019\Common\Utility\ADInf\targetver.h  
1>Note: including file: H:\Svn\branches\MainBuild_2019\Common\include\Global/global.h  
1>Note: including file:   C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\shared\sdkddkver.h  
1>Note: including file: H:\Svn\branches\MainBuild_2019\Common\include\Global/globalext_unix.h  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/bimap.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/bimap/bimap.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/config.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/config/user.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/config/detail/select_compiler_config.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/config/compiler/visualc.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/config/pragma_message.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/config/helper_macros.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/config/detail/select_stdlib_config.hpp  
1>Note: including file:      C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\version  
1>Note: including file:       C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\yvals_core.h  
1>Note: including file:        C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\vcruntime.h  
1>Note: including file:         C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\sal.h  
1>Note: including file:          C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\concurrencysal.h  
1>Note: including file:         C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\vadefs.h  
1>Note: including file:        C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\xkeycheck.h  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/config/stdlib/dinkumware.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/config/detail/select_platform_config.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/config/platform/win32.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/config/detail/suffix.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/bimap/detail/user_interface_config.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/na.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/bool.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/bool_fwd.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/adl_barrier.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/adl.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/msvc.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/intel.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/gcc.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/workaround.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/detail/workaround.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/config/workaround.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/integral_c_tag.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/static_constant.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/na_fwd.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/ctps.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/serialization/nvp.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/core/nvp.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/core/addressof.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/stringize.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/config/config.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/serialization/level.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_fundamental.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_arithmetic.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_integral.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/integral_constant.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_floating_point.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_void.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_enum.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/intrinsics.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/detail/config.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/version.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_array.hpp  
1>Note: including file:       C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\cstddef  
1>Note: including file:        C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\stddef.h  
1>Note: including file:         C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\corecrt.h  
1>Note: including file:        C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\xtr1common  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_class.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_base_and_derived.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/remove_cv.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_same.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/eval_if.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/if.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/value_wknd.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/static_cast.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/integral.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/eti.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/na_spec.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/lambda_fwd.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/void_fwd.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/lambda.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/ttp.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/int.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/int_fwd.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/nttp_decl.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/nttp.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/integral_wrapper.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/cat.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/lambda_arity_param.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/template_arity_fwd.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/arity.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/dtp.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/preprocessor/params.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/preprocessor.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/comma_if.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/punctuation/comma_if.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/control/if.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/control/iif.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/logical/bool.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/facilities/empty.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/punctuation/comma.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/repeat.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/repetition/repeat.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/debug/error.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/detail/auto_rec.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/tuple/eat.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/inc.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/arithmetic/inc.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/preprocessor/enum.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/preprocessor/def_params_tail.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/limits/arity.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/logical/and.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/logical/bitand.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/identity.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/facilities/identity.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/empty.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/arithmetic/add.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/arithmetic/dec.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/control/while.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/detail/auto_rec.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/list/fold_left.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/control/while.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/detail/auto_rec.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/list/detail/fold_left.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/control/expr_iif.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/list/adt.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/detail/is_binary.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/detail/check.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/logical/compl.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/list/fold_right.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/control/while.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/detail/auto_rec.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/list/detail/fold_right.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/list/reverse.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/control/detail/msvc/while.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/tuple/elem.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/facilities/expand.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/facilities/overload.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/variadic/size.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/facilities/check_empty.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/variadic/has_opt.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/tuple/rem.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/tuple/detail/is_single_return.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/facilities/is_1.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/facilities/is_empty.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/facilities/is_empty_variadic.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/punctuation/is_begin_parens.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/punctuation/detail/is_begin_parens.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/facilities/detail/is_empty.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/tuple/size.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/variadic/elem.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/arithmetic/sub.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/overload_resolution.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/lambda_support.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/integral_c.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/integral_c_fwd.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/integral_wrapper.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/serialization/level_enum.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/serialization/tracking.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/static_assert.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/identity.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/equal_to.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/comparison_op.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/numeric_op.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/numeric_cast.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/apply_wrap.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/has_apply.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/has_xxx.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/type_wrapper.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/yes_no.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/arrays.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/has_xxx.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/msvc_typename.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/array/elem.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/array/data.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/array/size.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/repetition/enum_params.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/preprocessor/repetition/enum_trailing_params.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/has_apply.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/msvc_never_true.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/use_preprocessed.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/include_preprocessed.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/compiler.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/preprocessed/plain/apply_wrap.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/tag.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/void.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/has_tag.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/numeric_cast_utils.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/apply_wrap.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/config/forwarding.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/msvc_eti_base.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/is_msvc_eti_arg.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/include_preprocessed.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/preprocessed/plain/equal_to.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/greater.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/comparison_op.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/numeric_op.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/apply_wrap.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/include_preprocessed.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/mpl/aux_/preprocessed/plain/greater.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_pointer.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/serialization/tracking_enum.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/serialization/type_info_implementation.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/serialization/traits.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/serialization/split_free.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/serialization/serialization.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/serialization/strong_typedef.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/operators.hpp  
1>Note: including file:         C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\iterator  
1>Note: including file:          C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\iosfwd  
1>Note: including file:           C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\yvals.h  
1>Note: including file:            C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\crtdbg.h  
1>Note: including file:             C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\vcruntime_new_debug.h  
1>Note: including file:              C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\vcruntime_new.h  
1>Note: including file:            C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\crtdefs.h  
1>Note: including file:            C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\use_ansi.h  
1>Note: including file:           C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\cstdio  
1>Note: including file:            C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\stdio.h  
1>Note: including file:             C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\corecrt_wstdio.h  
1>Note: including file:              C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\corecrt_stdio_config.h  
1>Note: including file:           C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\cstring  
1>Note: including file:            C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\string.h  
1>Note: including file:             C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\corecrt_memory.h  
1>Note: including file:              C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\corecrt_memcpy_s.h  
1>Note: including file:               C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\errno.h  
1>Note: including file:               C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\vcruntime_string.h  
1>Note: including file:             C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\corecrt_wstring.h  
1>Note: including file:           C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\cwchar  
1>Note: including file:            C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\wchar.h  
1>Note: including file:             C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\corecrt_wconio.h  
1>Note: including file:             C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\corecrt_wctype.h  
1>Note: including file:             C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\corecrt_wdirect.h  
1>Note: including file:             C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\corecrt_wio.h  
1>Note: including file:              C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\corecrt_share.h  
1>Note: including file:             C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\corecrt_wprocess.h  
1>Note: including file:             C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\corecrt_wstdlib.h  
1>Note: including file:             C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\corecrt_wtime.h  
1>Note: including file:             C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\sys/stat.h  
1>Note: including file:              C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\sys/types.h  
1>Note: including file:           C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\xstddef  
1>Note: including file:            C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\cstdlib  
1>Note: including file:             C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\math.h  
1>Note: including file:              C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\corecrt_math.h  
1>Note: including file:             C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\stdlib.h  
1>Note: including file:              C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\corecrt_malloc.h  
1>Note: including file:              C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt\corecrt_search.h  
1>Note: including file:              C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\limits.h  
1>Note: including file:            C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\initializer_list  
1>Note: including file:          C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\xutility  
1>Note: including file:           C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\climits  
1>Note: including file:           C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\utility  
1>Note: including file:            C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\type_traits  
1>Note: including file:             C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\cstdint  
1>Note: including file:              C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include\stdint.h  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/has_nothrow_assign.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/has_trivial_assign.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_pod.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_scalar.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_member_pointer.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_member_function_pointer.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/detail/is_member_function_pointer_cxx_11.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_const.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_volatile.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_assignable.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_complete.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/declval.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/add_rvalue_reference.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_reference.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_lvalue_reference.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_rvalue_reference.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/remove_reference.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_function.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/detail/is_function_cxx_11.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/detail/yes_no_type.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/add_reference.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/has_nothrow_constructor.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/has_trivial_constructor.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/is_default_constructible.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/has_nothrow_copy.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/type_traits/has_trivial_copy.hpp  
1>Note: including file: H:\Svn\ThirdParty-2019\boost_1_75_0\boost/serialization/access.hpp  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\operators.hpp(161,1): fatal error C1001: Internal compiler error.  
1>(compiler file 'msc1.cpp', line 1588)  
1> To work around this problem, try simplifying or changing the program near the locations listed above.  
1>If possible please provide a repro here: https://developercommunity.visualstudio.com  
1>Please choose the Technical Support command on the Visual C++  
1> Help menu, or open the Technical Support help file for more information  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\operators.hpp(141): message : see reference to class template instantiation 'boost::operators_impl::equality_comparable2<T,U,B>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type,  
1>            U=unsigned int,  
1> B=boost::operators_impl::operators_detail::empty_base<boost::serialization::version_type>  
1>        ]  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\operators.hpp(421): message : see reference to class template instantiation   
'boost::operators_impl::less_than_comparable2<T,U,boost::operators_impl::equality_comparable2<T,U,B>>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type,  
1>            U=unsigned int,  
1> B=boost::operators_impl::operators_detail::empty_base<boost::serialization::version_type>  
1>        ]  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\operators.hpp(168): message : see reference to class template instantiation   
'boost::operators_impl::totally_ordered2<boost::serialization::version_type,unsigned int,boost::operators_impl::operators_detail::empty_base<T>>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type  
1>        ]  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\operators.hpp(152): message : see reference to class template instantiation 'boost::operators_impl::equality_comparable1<T,B>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type,  
1> B=boost::operators_impl::totally_ordered2<boost::serialization::version_type,unsigned int,boost::operators_impl::operators_detail::empty_base<boost::serialization::version_type>>  
1>        ]  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\operators.hpp(427): message : see reference to class template instantiation   
'boost::operators_impl::less_than_comparable1<T,boost::operators_impl::equality_comparable1<T,B>>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type,  
1> B=boost::operators_impl::totally_ordered2<boost::serialization::version_type,unsigned int,boost::operators_impl::operators_detail::empty_base<boost::serialization::version_type>>  
1>        ]  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\serialization\serialization.hpp(52): message : see reference to class template instantiation   
'boost::operators_impl::totally_ordered1<boost::serialization::version_type,boost::operators_impl::totally_ordered2<boost::serialization::version_type,unsigned   
int,boost::operators_impl::operators_detail::empty_base<T>>>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type  
1>        ]  
1>INTERNAL COMPILER ERROR in 'C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\CL.exe'  
1>    Please choose the Technical Support command on the Visual C++  
1>    Help menu, or open the Technical Support help file for more information  
1>Done building project "ADInf.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
  
  
On 4/13/21 8:45 PM, Jonathan Caves wrote:  
>  
> Hi: some questions: which 2019 compiler? Using the latest build, the just about to be released 16.10 toolset, the most recent 16.9 and 16.8 toolsets I am not seeing any crash. Also the exact   
> command-line would also be helpful.  
>  
> Thanks  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/bimap/issues/29#issuecomment-819142005>, or unsubscribe   
> <https://github.com/notifications/unsubscribe-auth/ABD5MIIXDJ5HX5TVCGXIFBDTITQQ3ANCNFSM424EP5OQ>.  
>

---

## Comment 3

> Username: dgplayer  
> Created at: 2021-04-14 02:15:35 UTC  
> Url: https://github.com/boostorg/bimap/issues/29#issuecomment-819171539  

Project Properties:  
see attachment  
  
I tried to turn off advanced features 1 by 1 but no luck...  
  
On 4/13/21 8:45 PM, Jonathan Caves wrote:  
>  
> Hi: some questions: which 2019 compiler? Using the latest build, the just about to be released 16.10 toolset, the most recent 16.9 and 16.8 toolsets I am not seeing any crash. Also the exact   
> command-line would also be helpful.  
>  
> Thanks  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/bimap/issues/29#issuecomment-819142005>, or unsubscribe   
> <https://github.com/notifications/unsubscribe-auth/ABD5MIIXDJ5HX5TVCGXIFBDTITQQ3ANCNFSM424EP5OQ>.  
>

---

## Comment 4

> Username: dgplayer  
> Created at: 2021-04-14 02:31:07 UTC  
> Url: https://github.com/boostorg/bimap/issues/29#issuecomment-819176497  

VM Configuration

---

## Comment 5

> Username: dgplayer  
> Created at: 2021-04-14 02:34:14 UTC  
> Url: https://github.com/boostorg/bimap/issues/29#issuecomment-819177398  

Microsoft Visual Studio Professional 2019  
Version 16.9.4  
VisualStudio.16.Release/16.9.4+31205.134  
Microsoft .NET Framework  
Version 4.8.03761  
  
Installed Version: Professional  
  
Visual C++ 2019   00435-20052-57559-AA053  
Microsoft Visual C++ 2019  
  
ASP.NET and Web Tools 2019   16.9.693.2781  
ASP.NET and Web Tools 2019  
  
ASP.NET Core Razor Language Services 16.1.0.2112521+5741df381174d72f08e3632bb99f52e8635b6a1a  
Provides languages services for ASP.NET Core Razor.  
  
ASP.NET Web Frameworks and Tools 2012   16.9.693.2781  
For additional information, visit https://www.asp.net/  
  
ASP.NET Web Frameworks and Tools 2019   16.9.693.2781  
For additional information, visit https://www.asp.net/  
  
Azure App Service Tools v3.0.0   16.9.693.2781  
Azure App Service Tools v3.0.0  
  
C# Tools 3.9.0-6.21160.10+59eedc33d35754759994155ea2f4e1012a9951e3  
C# components used in the IDE. Depending on your project type and settings, a different version of the compiler may be used.  
  
Common Azure Tools   1.10  
Provides common services for use by Azure Mobile Services and Microsoft Azure Tools.  
  
IncrediBuild Build Acceleration   1.5.0.13  
IncrediBuild effectively reduces compilation and development times by up to 90%.  
  
IntelliCode Extension   1.0  
IntelliCode Visual Studio Extension Detailed Info  
  
Linux Core Dump Debugging   1.0.9.31201  
Enables debugging of Linux core dumps.  
  
Microsoft Continuous Delivery Tools for Visual Studio   0.4  
Simplifying the configuration of Azure DevOps pipelines from within the Visual Studio IDE.  
  
Microsoft JVM Debugger   1.0  
Provides support for connecting the Visual Studio debugger to JDWP compatible Java Virtual Machines  
  
Microsoft Library Manager   2.1.113+g422d40002e.RR  
Install client-side libraries easily to any web project  
  
Microsoft MI-Based Debugger   1.0  
Provides support for connecting Visual Studio to MI compatible debuggers  
  
Microsoft Visual C++ Wizards   1.0  
Microsoft Visual C++ Wizards  
  
Microsoft Visual Studio Tools for Containers   1.1  
Develop, run, validate your ASP.NET Core applications in the target environment. F5 your application directly into a container with debugging, or CTRL + F5 to edit & refresh your app without having to   
rebuild the container.  
  
Microsoft Visual Studio VC Package   1.0  
Microsoft Visual Studio VC Package  
  
NuGet Package Manager   5.9.0  
NuGet Package Manager in Visual Studio. For more information about NuGet, visit https://docs.nuget.org/  
  
Office Developer Tools for Visual Studio   16.0.30502.00  
Microsoft Office Developer Tools for Visual Studio  
  
ProjectServicesPackage Extension   1.0  
ProjectServicesPackage Visual Studio Extension Detailed Info  
  
SQL Server Data Tools   16.0.62103.10080  
Microsoft SQL Server Data Tools  
  
Syntax Visualizer   1.0  
An extension for visualizing Roslyn SyntaxTrees.  
  
Test Adapter for Boost.Test   1.0  
Enables Visual Studio's testing tools with unit tests written for Boost.Test.  The use terms and Third Party Notices are available in the extension installation directory.  
  
Test Adapter for Google Test   1.0  
Enables Visual Studio's testing tools with unit tests written for Google Test.  The use terms and Third Party Notices are available in the extension installation directory.  
  
TypeScript Tools   16.0.30201.2001  
TypeScript Tools for Microsoft Visual Studio  
  
Visual Basic Tools 3.9.0-6.21160.10+59eedc33d35754759994155ea2f4e1012a9951e3  
Visual Basic components used in the IDE. Depending on your project type and settings, a different version of the compiler may be used.  
  
Visual C++ for Linux Development   1.0.9.31201  
Visual C++ for Linux Development  
  
Visual F# Tools 16.9.0-beta.21102.9+7ce7132f1459095e635194d09d6f73265352029a  
Microsoft Visual F# Tools  
  
Visual Studio Code Debug Adapter Host Package   1.0  
Interop layer for hosting Visual Studio Code debug adapters in Visual Studio  
  
Visual Studio Container Tools Extensions   1.0  
View, manage, and diagnose containers within Visual Studio.  
  
Visual Studio Tools for CMake   1.0  
Visual Studio Tools for CMake  
  
Visual Studio Tools for Containers   1.0  
Visual Studio Tools for Containers  
  
Visual Studio Tools for Kubernetes   1.0  
Visual Studio Tools for Kubernetes  
  
Workflow Manager Tools 1.0   1.0  
  
  
This package contains the necessary Visual Studio integration components for Workflow Manager.  
  
On 4/13/21 8:45 PM, Jonathan Caves wrote:Hi: some questions: which 2019 compiler? Using the latest build, the just about to be released 16.10 toolset, the most recent 16.9 and 16.8 toolsets I am not   
seeing any crash. Also the exact command-line would also be helpful.  
>  
> Thanks  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/bimap/issues/29#issuecomment-819142005>, or unsubscribe   
> <https://github.com/notifications/unsubscribe-auth/ABD5MIIXDJ5HX5TVCGXIFBDTITQQ3ANCNFSM424EP5OQ>.  
>

---

## Comment 6

> Username: JonCavesMSFT  
> Created at: 2021-04-14 13:32:35 UTC  
> Url: https://github.com/boostorg/bimap/issues/29#issuecomment-819521109  

Hi: thanks for the information but it is not quite what I need. Try adding /Bd /Bv to the command line (and remove /showIncludes) and append that output. I just tried the exact build you reference above, 19.28.29910.0, with a pretty vanilla command line and it built without a crash. Note: I also don't have access to the header files you #include before <boost\bimap.hpp>  
  
Thanks  
JonCaves

---

## Comment 7

> Username: dgplayer  
> Created at: 2021-04-14 15:41:47 UTC  
> Url: https://github.com/boostorg/bimap/issues/29#issuecomment-819618084  

// stdafx.cpp : source file that includes just the standard includes  
// ADInf.pch will be the pre-compiled header  
// stdafx.obj will contain the pre-compiled type information  
  
#include "stdafx.h"  
  
// TODO: reference any additional headers you need in STDAFX.H  
// and not in this file  
  
  
  
  
// stdafx.h : include file for standard system include files,  
// or project specific include files that are used frequently, but  
// are changed infrequently  
//  
#pragma once  
  
#include "targetver.h"         // Set WINVER=0x600, _WIN32_WINNT=0x0600, _WIN32_WINDOWS=0x0600  
#include <boost/bimap.hpp>  
  
#include "Global/global.h"  
#include "Global/globalext_unix.h"  
  
#include <string>  
#include <set>  
#include <map>  
  
#define WIN32_LEAN_AND_MEAN    // Exclude rarely-used stuff from Windows headers  
  
#if defined(DO_WIN)  
#include <afx.h>  
#include <afxwin.h>   // MFC core and standard components  
#include <afxtempl.h> // CArray, CList, CMap, CTypedPtrArray, CTypedPtrList, CTypedPtrMap  
#include <afxstr.h>   // MFC CString  
#else  
#include "Global/WinDefines.h"  
#endif  
  
// Windows Header Files:  
  
#include "Global/ADInf.h"  
  
using namespace std;  
  
  
  
  
  
Build started...  
1>------ Build started: Project: ADInf, Configuration: Debug x64 ------  
1>cl : command line warning D9035: option 'Gm' has been deprecated and will be removed in a future release  
1>Compiler Passes:  
1> C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\CL.exe: Version 19.28.29914.0  
1> C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\c1.dll: Version 19.28.29914.0  
1> C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\c1xx.dll: Version 19.28.29914.0  
1> C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\c2.dll: Version 19.28.29914.0  
1> C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\c1xx.dll: Version 19.28.29914.0  
1> C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\link.exe: Version 14.28.29914.0  
1> C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\mspdb140.dll: Version 14.28.29914.0  
1> C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\1033\clui.dll: Version 19.28.29914.0  
1>stdafx.cpp  
1>ENC_CWD=H:\Svn\branches\MainBuild_2019\Common\Utility\ADInf  
1>ENC_CL=C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\CL.exe  
1>ENC_SRC=stdafx.cpp  
1>ENC_PDB=H:\Svn\branches\MainBuild_2019\Common\Utility\ADInf\Debug\x64\vc142.pdb  
1>ENC_CMD=-c -IH:\Svn\branches\MainBuild_2019\Common\Utility\ADInf\ -IH:\Svn\branches\MainBuild_2019\Common\include -IH:\Svn\branches\MainBuild_2019\Common\Classes   
-IH:\Svn\ThirdParty-2019\boost_1_75_0 -Zi -nologo -W3 -WX- -diagnostics:column -Od -DWIN32 -D_DEBUG -D_WINDOWS -D_USRDLL -DADINF_EXPORTS -DSKIP_SCREEN_PRINT -D_WINDLL -D_MBCS -D_AFXDLL -Gm -EHs -EHc   
-RTC1 -MDd -GS -fp:precise -permissive -Zc:wchar_t -Zc:forScope -Zc:rvalueCast- -GR -openmp- -Ycstdafx.h -FpH:\Svn\branches\MainBuild_2019\Common\Utility\ADInf\Debug\x64\ADInf.pch -Gd -TP -FC   
-errorreport:prompt -Bd -Bv -I"C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include" -I"C:\Program Files (x86)\Microsoft Visual   
Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\atlmfc\include" -I"C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Auxiliary\VS\include" -I"C:\Program Files (x86)\Windows   
Kits\10\Include\10.0.18362.0\ucrt" -I"C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Auxiliary\VS\UnitTest\include" -I"C:\Program Files (x86)\Windows   
Kits\10\Include\10.0.18362.0\um" -I"C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\shared" -I"C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\winrt" -I"C:\Program Files   
(x86)\Windows Kits\10\Include\10.0.18362.0\cppwinrt" -I"C:\Program Files (x86)\Windows Kits\NETFXSDK\4.8\Include\um" -X  
1>`C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\c1xx.dll -zm0x0122B1C0 -il C:\Users\dgodfrey\AppData\Local\Temp\_CL_f8824d2c -typedil -f   
stdafx.cpp -Ze -D_MSC_EXTENSIONS -Zp16 -pc \:/ -D_MSC_VER=1928 -D_MSC_FULL_VER=192829914 -D_MSC_BUILD=0 -D_M_AMD64=100 -ZILP448 -D_M_X64=100 -D_WIN64 -D_WIN32 -I   
H:\Svn\branches\MainBuild_2019\Common\Utility\ADInf\ -I H:\Svn\branches\MainBuild_2019\Common\include -I H:\Svn\branches\MainBuild_2019\Common\Classes -I H:\Svn\ThirdParty-2019\boost_1_75_0 -Zi   
-nologo -W 3 -diagnostics:column -Ot -DWIN32 -D_DEBUG -D_WINDOWS -D_USRDLL -DADINF_EXPORTS -DSKIP_SCREEN_PRINT -D_WINDLL -D_MBCS -D_AFXDLL -Gm -EHs -D_CPPUNWIND -EHc -D__MSVC_RUNTIME_CHECKS -RTCs   
-RTCu -MDd -D_DEBUG -D_MT -D_DLL -GS -D_M_FP_PRECISE -permissive -Zc:wchar_t -Zc:forScope -Zc:rvalueCast- -GR -D_CPPRTTI -Ycstdafx.h -Fp.\Debug\x64\ADInf.pch -Fo.\Debug\x64\stdafx.obj   
-Fd.\Debug\x64\vc142.pdb -Gd -FC -errorreport:prompt -Bd -Bv -I C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include -I C:\Program Files (x86)\Microsoft   
Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\atlmfc\include -I C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Auxiliary\VS\include -I C:\Program Files (x86)\Windows   
Kits\10\Include\10.0.18362.0\ucrt -I C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Auxiliary\VS\UnitTest\include -I C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um   
-I C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\shared -I C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\winrt -I C:\Program Files (x86)\Windows   
Kits\10\Include\10.0.18362.0\cppwinrt -I C:\Program Files (x86)\Windows Kits\NETFXSDK\4.8\Include\um'  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\operators.hpp(161,1): fatal error C1001: Internal compiler error.  
1>(compiler file 'msc1.cpp', line 1588)  
1> To work around this problem, try simplifying or changing the program near the locations listed above.  
1>If possible please provide a repro here: https://developercommunity.visualstudio.com  
1>Please choose the Technical Support command on the Visual C++  
1> Help menu, or open the Technical Support help file for more information  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\operators.hpp(141): message : see reference to class template instantiation 'boost::operators_impl::equality_comparable2<T,U,B>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type,  
1>            U=unsigned int,  
1> B=boost::operators_impl::operators_detail::empty_base<boost::serialization::version_type>  
1>        ]  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\operators.hpp(421): message : see reference to class template instantiation   
'boost::operators_impl::less_than_comparable2<T,U,boost::operators_impl::equality_comparable2<T,U,B>>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type,  
1>            U=unsigned int,  
1> B=boost::operators_impl::operators_detail::empty_base<boost::serialization::version_type>  
1>        ]  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\operators.hpp(168): message : see reference to class template instantiation   
'boost::operators_impl::totally_ordered2<boost::serialization::version_type,unsigned int,boost::operators_impl::operators_detail::empty_base<T>>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type  
1>        ]  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\operators.hpp(152): message : see reference to class template instantiation 'boost::operators_impl::equality_comparable1<T,B>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type,  
1> B=boost::operators_impl::totally_ordered2<boost::serialization::version_type,unsigned int,boost::operators_impl::operators_detail::empty_base<boost::serialization::version_type>>  
1>        ]  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\operators.hpp(427): message : see reference to class template instantiation   
'boost::operators_impl::less_than_comparable1<T,boost::operators_impl::equality_comparable1<T,B>>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type,  
1> B=boost::operators_impl::totally_ordered2<boost::serialization::version_type,unsigned int,boost::operators_impl::operators_detail::empty_base<boost::serialization::version_type>>  
1>        ]  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\serialization\serialization.hpp(52): message : see reference to class template instantiation   
'boost::operators_impl::totally_ordered1<boost::serialization::version_type,boost::operators_impl::totally_ordered2<boost::serialization::version_type,unsigned   
int,boost::operators_impl::operators_detail::empty_base<T>>>' being compiled  
1>        with  
1>        [  
1>            T=boost::serialization::version_type  
1>        ]  
1>INTERNAL COMPILER ERROR in 'C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\CL.exe'  
1>    Please choose the Technical Support command on the Visual C++  
1>    Help menu, or open the Technical Support help file for more information  
1>Done building project "ADInf.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========

---

## Comment 8

> Username: JonCavesMSFT  
> Created at: 2021-04-14 16:01:53 UTC  
> Url: https://github.com/boostorg/bimap/issues/29#issuecomment-819632488  

Perfect! That allows me to see exactly what the problem is: it is the use of '/Gm' (AKA Minimal Rebuild). The use of the switch already gives a driver warning:  
  
cl : Command line warning D9035 : option 'Gm' has been deprecated and will be removed in a future release  
  
And just last week I disabled it in the 'live' compiler. If you remove this switch your code should compile.  
  
Thanks  
JonCaves

---

## Comment 9

> Username: dgplayer  
> Created at: 2021-04-14 16:26:58 UTC  
> Url: https://github.com/boostorg/bimap/issues/29#issuecomment-819649740  

No crash, but multiple template instantiation errors  
  
Build started...  
1>------ Build started: Project: ADInf, Configuration: Debug x64 ------  
1>Compiler Passes:  
1> C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\CL.exe: Version 19.28.29914.0  
1> C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\c1.dll: Version 19.28.29914.0  
1> C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\c1xx.dll: Version 19.28.29914.0  
1> C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\c2.dll: Version 19.28.29914.0  
1> C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\c1xx.dll: Version 19.28.29914.0  
1> C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\link.exe: Version 14.28.29914.0  
1> C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\mspdb140.dll: Version 14.28.29914.0  
1> C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\1033\clui.dll: Version 19.28.29914.0  
1>stdafx.cpp  
1>ENC_CWD=H:\Svn\branches\MainBuild_2019\Common\Utility\ADInf  
1>ENC_CL=C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\CL.exe  
1>ENC_SRC=stdafx.cpp  
1>ENC_PDB=H:\Svn\branches\MainBuild_2019\Common\Utility\ADInf\Debug\x64\vc142.pdb  
1>ENC_CMD=-c -IH:\Svn\branches\MainBuild_2019\Common\Utility\ADInf\ -IH:\Svn\branches\MainBuild_2019\Common\include -IH:\Svn\branches\MainBuild_2019\Common\Classes   
-IH:\Svn\ThirdParty-2019\boost_1_75_0 -Zi -nologo -W3 -WX- -diagnostics:column -Od -Ob1 -Oi -Os -Oy- -GT -DWIN32 -D_DEBUG -D_WINDOWS -D_USRDLL -DADINF_EXPORTS -DSKIP_SCREEN_PRINT -D_WINDLL -D_MBCS   
-D_AFXDLL -Gm- -EHs -EHc -RTC1 -MDd -GS -fp:precise -permissive -Zc:wchar_t -Zc:forScope -Zc:rvalueCast- -GR -openmp- -Ycstdafx.h   
-FpH:\Svn\branches\MainBuild_2019\Common\Utility\ADInf\Debug\x64\ADInf.pch -Gd -TP -FC -errorreport:prompt -Bd -Bv -I"C:\Program Files (x86)\Microsoft Visual   
Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include" -I"C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\atlmfc\include" -I"C:\Program Files   
(x86)\Microsoft Visual Studio\2019\Professional\VC\Auxiliary\VS\include" -I"C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\ucrt" -I"C:\Program Files (x86)\Microsoft Visual   
Studio\2019\Professional\VC\Auxiliary\VS\UnitTest\include" -I"C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um" -I"C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\shared"   
-I"C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\winrt" -I"C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\cppwinrt" -I"C:\Program Files (x86)\Windows   
Kits\NETFXSDK\4.8\Include\um" -X  
1>`C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\c1xx.dll -zm0x0125B1C0 -il C:\Users\dgodfrey\AppData\Local\Temp\_CL_46b22f2e -typedil -f   
stdafx.cpp -Ze -D_MSC_EXTENSIONS -Zp16 -pc \:/ -D_MSC_VER=1928 -D_MSC_FULL_VER=192829914 -D_MSC_BUILD=0 -D_M_AMD64=100 -ZILP448 -D_M_X64=100 -D_WIN64 -D_WIN32 -I   
H:\Svn\branches\MainBuild_2019\Common\Utility\ADInf\ -I H:\Svn\branches\MainBuild_2019\Common\include -I H:\Svn\branches\MainBuild_2019\Common\Classes -I H:\Svn\ThirdParty-2019\boost_1_75_0 -Zi   
-nologo -W 3 -diagnostics:column -Oi -Os -DWIN32 -D_DEBUG -D_WINDOWS -D_USRDLL -DADINF_EXPORTS -DSKIP_SCREEN_PRINT -D_WINDLL -D_MBCS -D_AFXDLL -EHs -D_CPPUNWIND -EHc -D__MSVC_RUNTIME_CHECKS -RTCs   
-RTCu -MDd -D_DEBUG -D_MT -D_DLL -GS -D_M_FP_PRECISE -permissive -Zc:wchar_t -Zc:forScope -Zc:rvalueCast- -GR -D_CPPRTTI -Ycstdafx.h -Fp.\Debug\x64\ADInf.pch -Fo.\Debug\x64\stdafx.obj   
-Fd.\Debug\x64\vc142.pdb -Gd -FC -errorreport:prompt -Bd -Bv -I C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\include -I C:\Program Files (x86)\Microsoft   
Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\atlmfc\include -I C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Auxiliary\VS\include -I C:\Program Files (x86)\Windows   
Kits\10\Include\10.0.18362.0\ucrt -I C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Auxiliary\VS\UnitTest\include -I C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um   
-I C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\shared -I C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\winrt -I C:\Program Files (x86)\Windows   
Kits\10\Include\10.0.18362.0\cppwinrt -I C:\Program Files (x86)\Windows Kits\NETFXSDK\4.8\Include\um'  
1>ADINF_API __declspec(dllexport)  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\concept_check.hpp(355,12): warning C4834: discarding return value of function with 'nodiscard' attribute  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\concept_check.hpp(354): message : while compiling class template member function 'void boost::BinaryFunction<Func,Return,First,Second>::test(boost::false_type)'  
1>        with  
1>        [  
1>            Func=std::less<std::string>,  
1>            Return=bool,  
1> First=std::basic_string<char,std::char_traits<char>,std::allocator<char>>,  
1> Second=std::basic_string<char,std::char_traits<char>,std::allocator<char>>  
1>        ]  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\concept_check.hpp(351): message : see reference to function template instantiation 'void   
boost::BinaryFunction<Func,Return,First,Second>::test(boost::false_type)' being compiled  
1>        with  
1>        [  
1>            Func=std::less<std::string>,  
1>            Return=bool,  
1> First=std::basic_string<char,std::char_traits<char>,std::allocator<char>>,  
1> Second=std::basic_string<char,std::char_traits<char>,std::allocator<char>>  
1>        ]  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\concept_check.hpp(349): message : see reference to class template instantiation 'boost::BinaryFunction<Func,Return,First,Second>' being compiled  
1>        with  
1>        [  
1>            Func=std::less<std::string>,  
1>            Return=bool,  
1> First=std::basic_string<char,std::char_traits<char>,std::allocator<char>>,  
1> Second=std::basic_string<char,std::char_traits<char>,std::allocator<char>>  
1>        ]  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\concept\detail\has_constraints.hpp(42): message : see reference to class template instantiation   
'boost::BinaryFunctionConcept<std::less<std::string>,bool,std::basic_string<char,std::char_traits<char>,std::allocator<char>>,std::basic_string<char,std::char_traits<char>,std::allocator<char>>>'   
being compiled  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\concept\detail\has_constraints.hpp(32): message : see reference to function template instantiation 'boost::concepts::detail::yes   
boost::concepts::detail::has_constraints_(Model *,boost::concepts::detail::wrap_constraints<Model,&Model::constraints> *)' being compiled  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\concept\detail\msvc.hpp(57): message : see reference to class template instantiation 'boost::concepts::not_satisfied<Model>' being compiled  
1>        with  
1>        [  
1> Model=boost::BinaryFunctionConcept<std::less<std::string>,bool,std::basic_string<char,std::char_traits<char>,std::allocator<char>>,std::basic_string<char,std::char_traits<char>,std::allocator<char>>>  
1>        ]  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\bimap\set_of.hpp(132): message : see reference to class template instantiation   
'boost::concepts::require<boost::BinaryFunctionConcept<std::less<std::string>,bool,std::basic_string<char,std::char_traits<char>,std::allocator<char>>,std::basic_string<char,std::char_traits<char>,std::allocator<char>>>>'   
being compiled  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\bimap\detail\manage_bimap_key.hpp(69): message : see reference to class template instantiation   
'boost::bimaps::set_of<Type,std::less<std::string>>::lazy_concept_checked' being compiled  
1>        with  
1>        [  
1>            Type=std::string  
1>        ]  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\bimap\detail\bimap_core.hpp(91): message : see reference to class template instantiation 'boost::bimaps::detail::manage_bimap_key<LeftSetType>' being compiled  
1>        with  
1>        [  
1>            LeftSetType=std::string  
1>        ]  
1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\bimap\bimap.hpp(134): message : see reference to class template instantiation 'boost::bimaps::detail::bimap_core<KeyTypeA,KeyTypeB,AP1,AP2,AP3>' being compiled  
1>        with  
1>        [  
1>            KeyTypeA=std::string,  
1>            KeyTypeB=std::string,  
1>            AP1=boost::mpl::na,  
1>            AP2=boost::mpl::na,  
1>            AP3=boost::mpl::na  
1>        ]  
1>H:\Svn\branches\MainBuild_2019\Common\include\Global\ADInf.h(216): message : see reference to class template instantiation   
'boost::bimaps::bimap<std::string,std::string,boost::mpl::na,boost::mpl::na,boost::mpl::na>' being compiled  
1>`C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.28.29910\bin\HostX86\x64\c2.dll -il C:\Users\dgodfrey\AppData\Local\Temp\_CL_46b22f2e -typedil -f stdafx.cpp -dos   
-Zi -W 3 -Ob1 -Os -GT -EHs -MDd -GS -Yc -Fo.\Debug\x64\stdafx.obj -Fd.\Debug\x64\vc142.pdb -errorreport:prompt -Bd'  
1>Failed to connect to VCTIP: 'CreateFile' failed with 2  
1>Launching VCTIP  
1>Done building project "ADInf.vcxproj".  
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========  
  
  
On 4/14/21 12:02 PM, Jonathan Caves wrote:  
>  
> Perfect! That allows me to see exactly what the problem is: it is the use of '/Gm' (AKA Minimal Rebuild). The use of the switch already gives a driver warning:  
>  
> cl : Command line warning D9035 : option 'Gm' has been deprecated and will be removed in a future release  
>  
> And just last week I disabled it in the 'live' compiler. If you remove this switch your code should compile.  
>  
> Thanks  
> JonCaves  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub <https://github.com/boostorg/bimap/issues/29#issuecomment-819632488>, or unsubscribe   
> <https://github.com/notifications/unsubscribe-auth/ABD5MIONNGMRSUPAVD3ELCTTIW4AJANCNFSM424EP5OQ>.  
>

---

## Comment 10

> Username: JonCavesMSFT  
> Created at: 2021-04-14 16:32:19 UTC  
> Url: https://github.com/boostorg/bimap/issues/29#issuecomment-819654668  

That isn't an error it is a single warning (with several continuation messages showing the template instantiation stack) and, as warnings are not being treated as errors, the compile succeeds.  
  
I don't know what VCTIP is (or why the call to CreateFile failed) but it doesn't appear to have caused the build to fail.  
  
Thanks  
JonCaves

---

## Comment 11

> Username: mclow  
> Created at: 2021-04-14 16:41:40 UTC  
> Url: https://github.com/boostorg/bimap/issues/29#issuecomment-819660669  

And the warning for concept_check  
> 1>H:\Svn\ThirdParty-2019\boost_1_75_0\boost\concept_check.hpp(355,12): warning C4834: discarding return value of function with 'nodiscard' attribute  
  
has been fixed, and the fix will appear in the 1.76.0 release (which is imminent)
