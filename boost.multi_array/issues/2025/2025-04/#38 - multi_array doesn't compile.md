# #38 - multi_array doesn't compile [Open]

> Username: VasilyevAlexander  
> Created at: 2025-04-23 19:29:46 UTC  
> Updated at: 2025-04-30 13:18:35 UTC  
> Url: https://github.com/boostorg/multi_array/issues/38  

There are a lot of errors with simple multi_array instantiation:  
1. declaration of 'extents' hides global declaration  
2. deprecated assign method is used instead of fill in boost::array used for extents and storage_order in multi_array constructor. Problem with storage_order constructor and multi_array constructor itself.  
  
Microsoft (R) C/C++ Optimizing Compiler Version 19.43.34810 for x64  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
#include <boost/multi_array.hpp>  
  
GlobalFixture::GlobalFixture() {  
	boost::multi_array<double, 4> ma;  
}  
  
  
1>------ Build started: Project: mts_test_utility, Configuration: Debug x64 ------  
1>GlobalFixture.cpp  
1>C:\boost_1_88_0\boost\multi_array\multi_array_ref.hpp(615,45): warning C4459: declaration of 'extents' hides global declaration  
1>(compiling source file '/GlobalFixture.cpp')  
1>    C:\boost_1_88_0\boost\multi_array\base.hpp(69,33):  
1>    see declaration of 'boost::`anonymous-namespace'::extents'  
1>    C:\boost_1_88_0\boost\multi_array\multi_array_ref.hpp(615,45):  
1>    the template instantiation context (the oldest one first) is  
1>        C:\MTS\mts_test_utility\GlobalFixture.cpp(16,32):  
1>        see reference to class template instantiation 'boost::multi_array<double,4,std::allocator<double>>' being compiled  
1>        C:\boost_1_88_0\boost\multi_array.hpp(122,10):  
1>        see reference to class template instantiation 'boost::multi_array_ref<T,4>' being compiled  
1>        with  
1>        [  
1>            T=double  
1>        ]  
1>        C:\boost_1_88_0\boost\multi_array\multi_array_ref.hpp(612,3):  
1>        while compiling class template member function 'boost::multi_array_ref<T,4>::multi_array_ref(T *,const boost::general_storage_order<4> &,const __int64 *,const unsigned __int64 *)'  
1>        with  
1>        [  
1>            T=double  
1>        ]  
1>            C:\boost_1_88_0\boost\multi_array.hpp(153,15):  
1>            see the first reference to 'boost::multi_array_ref<T,4>::multi_array_ref' in 'boost::multi_array<double,4,std::allocator<double>>::multi_array'  
1>        with  
1>        [  
1>            T=double  
1>        ]  
1>            C:\MTS\mts_test_utility\GlobalFixture.cpp(16,32):  
1>            see the first reference to 'boost::multi_array<double,4,std::allocator<double>>::multi_array' in 'GlobalFixture::GlobalFixture'  
1>C:\boost_1_88_0\boost\multi_array\storage_order.hpp(51,18): error C4996: 'boost::array<bool,4>::assign': please use `fill` instead  
1>(compiling source file '/GlobalFixture.cpp')  
1>    C:\boost_1_88_0\boost\multi_array\storage_order.hpp(51,18):  
1>    the template instantiation context (the oldest one first) is  
1>        C:\boost_1_88_0\boost\multi_array\multi_array_ref.hpp(413,10):  
1>        see reference to class template instantiation 'boost::const_multi_array_ref<T,4,T *>' being compiled  
1>        with  
1>        [  
1>            T=double  
1>        ]  
1>        C:\boost_1_88_0\boost\multi_array\multi_array_ref.hpp(350,22):  
1>        see reference to class template instantiation 'boost::general_storage_order<4>' being compiled  
1>        C:\boost_1_88_0\boost\multi_array\storage_order.hpp(47,5):  
1>        while compiling class template member function 'boost::general_storage_order<4>::general_storage_order(const boost::c_storage_order &)'  
1>            C:\boost_1_88_0\boost\multi_array.hpp(153,15):  
1>            see the first reference to 'boost::general_storage_order<4>::general_storage_order' in 'boost::multi_array<double,4,std::allocator<double>>::multi_array'  
1>            C:\MTS\mts_test_utility\GlobalFixture.cpp(16,32):  
1>            see the first reference to 'boost::multi_array<double,4,std::allocator<double>>::multi_array' in 'GlobalFixture::GlobalFixture'  
1>C:\boost_1_88_0\boost\multi_array\multi_array_ref.hpp(328,42): warning C4459: declaration of 'extents' hides global declaration  
1>(compiling source file '/GlobalFixture.cpp')  
1>    C:\boost_1_88_0\boost\multi_array\base.hpp(69,33):  
1>    see declaration of 'boost::`anonymous-namespace'::extents'  
1>    C:\boost_1_88_0\boost\multi_array\multi_array_ref.hpp(328,42):  
1>    the template instantiation context (the oldest one first) is  
1>        C:\boost_1_88_0\boost\multi_array\multi_array_ref.hpp(324,3):  
1>        while compiling class template member function 'boost::const_multi_array_ref<T,4,T *>::const_multi_array_ref(TPtr,const boost::general_storage_order<4> &,const __int64 *,const unsigned __int64 *)'  
1>        with  
1>        [  
1>            T=double,  
1>            TPtr=double *  
1>        ]  
1>            C:\boost_1_88_0\boost\multi_array\multi_array_ref.hpp(616,15):  
1>            see the first reference to 'boost::const_multi_array_ref<T,4,T *>::const_multi_array_ref' in 'boost::multi_array_ref<T,4>::multi_array_ref'  
1>        with  
1>        [  
1>            T=double  
1>        ]  
1>            C:\boost_1_88_0\boost\multi_array.hpp(153,15):  
1>            see the first reference to 'boost::multi_array_ref<T,4>::multi_array_ref' in 'boost::multi_array<double,4,std::allocator<double>>::multi_array'  
1>        with  
1>        [  
1>            T=double  
1>        ]  
1>            C:\MTS\mts_test_utility\GlobalFixture.cpp(16,32):  
1>            see the first reference to 'boost::multi_array<double,4,std::allocator<double>>::multi_array' in 'GlobalFixture::GlobalFixture'  
1>C:\boost_1_88_0\boost\multi_array\multi_array_ref.hpp(343,18): error C4996: 'boost::array<__int64,4>::assign': please use `fill` instead  
1>(compiling source file '/GlobalFixture.cpp')  
1>Done building project "mts_test_utility.vcxproj" -- FAILED.

---

## Comment 1

> Username: gkellerer  
> Created at: 2025-04-30 13:18:33 UTC  
> Url: https://github.com/boostorg/multi_array/issues/38#issuecomment-2841946953  

i have got similar issues since V1.88.0 and with news VS Studio on Windows 11
