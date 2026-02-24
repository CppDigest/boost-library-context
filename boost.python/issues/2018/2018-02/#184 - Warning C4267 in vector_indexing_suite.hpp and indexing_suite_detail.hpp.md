# #184 - Warning C4267 in vector_indexing_suite.hpp and indexing_suite_detail.hpp [Open]

> Username: cnovel  
> Created at: 2018-02-02 15:09:09 UTC  
> Updated at: 2021-05-03 18:27:37 UTC  
> Url: https://github.com/boostorg/python/issues/184  

Usage of boost::python::vector_indexing_suite leads to a warning :  
`boost/python/suite/indexing/detail/indexing_suite_detail.hpp(619): warning C4267: '+=': conversion from 'size_t' to 'long', possible loss of data`  
  
Here's a snippet of code that produces the warning.  
```  
#pragma warning(push)  
#pragma warning(disable:4244)  
// See https://svn.boost.org/trac10/ticket/3353 for 4244  
#include <boost/python/suite/indexing/vector_indexing_suite.hpp>  
#pragma warning(pop)  
  
void test()  
{  
    boost::python::class_<std::vector<double>>("DoubleVec")  
        .def(boost::python::vector_indexing_suite<std::vector<double> >())  
        ;  
}  
```  
  
Here's the output of VS2017:  
```  
1>test.cpp  
1>C:\ThirdParty\1.8.0\win64-vc14\boost\include\boost-1_63\boost/python/suite/indexing/vector_indexing_suite.hpp(176): warning C4267: '+=': conversion from 'size_t' to 'long', possible loss of data  
1>C:\ThirdParty\1.8.0\win64-vc14\boost\include\boost-1_63\boost/python/suite/indexing/vector_indexing_suite.hpp(170): note: while compiling class template member function 'unsigned __int64 boost::python::vector_indexing_suite<std::vector<double,std::allocator<_Ty>>,false,boost::python::detail::final_vector_derived_policies<Container,false>>::convert_index(Container &,PyObject *)'  
1>        with  
1>        [  
1>            _Ty=double,  
1>            Container=std::vector<double,std::allocator<double>>  
1>        ]  
1>C:\ThirdParty\1.8.0\win64-vc14\boost\include\boost-1_63\boost/python/suite/indexing/indexing_suite.hpp(261): note: see reference to function template instantiation 'unsigned __int64 boost::python::vector_indexing_suite<std::vector<double,std::allocator<_Ty>>,false,boost::python::detail::final_vector_derived_policies<Container,false>>::convert_index(Container &,PyObject *)' being compiled  
1>        with  
1>        [  
1>            _Ty=double,  
1>            Container=std::vector<double,std::allocator<double>>  
1>        ]  
1>C:\Users\Cyril\dev\test.cpp(9): note: see reference to class template instantiation 'boost::python::vector_indexing_suite<std::vector<double,std::allocator<_Ty>>,false,boost::python::detail::final_vector_derived_policies<Container,false>>' being compiled  
1>        with  
1>        [  
1>            _Ty=double,  
1>            Container=std::vector<double,std::allocator<double>>  
1>        ]  
1>C:\ThirdParty\1.8.0\win64-vc14\boost\include\boost-1_63\boost/bind/placeholders.hpp(54): note: see reference to class template instantiation 'boost::arg<9>' being compiled  
1>C:\ThirdParty\1.8.0\win64-vc14\boost\include\boost-1_63\boost/bind/placeholders.hpp(53): note: see reference to class template instantiation 'boost::arg<8>' being compiled  
1>C:\ThirdParty\1.8.0\win64-vc14\boost\include\boost-1_63\boost/bind/placeholders.hpp(52): note: see reference to class template instantiation 'boost::arg<7>' being compiled  
1>C:\ThirdParty\1.8.0\win64-vc14\boost\include\boost-1_63\boost/bind/placeholders.hpp(51): note: see reference to class template instantiation 'boost::arg<6>' being compiled  
1>C:\ThirdParty\1.8.0\win64-vc14\boost\include\boost-1_63\boost/bind/placeholders.hpp(50): note: see reference to class template instantiation 'boost::arg<5>' being compiled  
1>C:\ThirdParty\1.8.0\win64-vc14\boost\include\boost-1_63\boost/bind/placeholders.hpp(49): note: see reference to class template instantiation 'boost::arg<4>' being compiled  
1>C:\ThirdParty\1.8.0\win64-vc14\boost\include\boost-1_63\boost/bind/placeholders.hpp(48): note: see reference to class template instantiation 'boost::arg<3>' being compiled  
1>C:\ThirdParty\1.8.0\win64-vc14\boost\include\boost-1_63\boost/bind/placeholders.hpp(47): note: see reference to class template instantiation 'boost::arg<2>' being compiled  
1>C:\ThirdParty\1.8.0\win64-vc14\boost\include\boost-1_63\boost/bind/placeholders.hpp(46): note: see reference to class template instantiation 'boost::arg<1>' being compiled  
1>C:\ThirdParty\1.8.0\win64-vc14\boost\include\boost-1_63\boost/python/suite/indexing/detail/indexing_suite_detail.hpp(605): warning C4267: '+=': conversion from 'size_t' to 'long', possible loss of data  
1>C:\ThirdParty\1.8.0\win64-vc14\boost\include\boost-1_63\boost/python/suite/indexing/detail/indexing_suite_detail.hpp(590): note: while compiling class template member function 'void boost::python::detail::slice_helper<Container,DerivedPolicies,boost::python::detail::no_proxy_helper<Container,DerivedPolicies,boost::python::detail::container_element<Container,Index,DerivedPolicies>,Index>,Data,Index>::base_get_slice_data(Container &,PySliceObject *,Index &,Index &)'  
1>        with  
1>        [  
1>            Container=std::vector<double,std::allocator<double>>,  
1>            DerivedPolicies=boost::python::detail::final_vector_derived_policies<std::vector<double,std::allocator<double>>,false>,  
1>            Index=unsigned __int64,  
1>            Data=double  
1>        ]  
1>C:\ThirdParty\1.8.0\win64-vc14\boost\include\boost-1_63\boost/python/suite/indexing/detail/indexing_suite_detail.hpp(695): note: see reference to function template instantiation 'void boost::python::detail::slice_helper<Container,DerivedPolicies,boost::python::detail::no_proxy_helper<Container,DerivedPolicies,boost::python::detail::container_element<Container,Index,DerivedPolicies>,Index>,Data,Index>::base_get_slice_data(Container &,PySliceObject *,Index &,Index &)' being compiled  
1>        with  
1>        [  
1>            Container=std::vector<double,std::allocator<double>>,  
1>            DerivedPolicies=boost::python::detail::final_vector_derived_policies<std::vector<double,std::allocator<double>>,false>,  
1>            Index=unsigned __int64,  
1>            Data=double  
1>        ]  
1>C:\ThirdParty\1.8.0\win64-vc14\boost\include\boost-1_63\boost/python/suite/indexing/indexing_suite.hpp(256): note: see reference to class template instantiation 'boost::python::detail::slice_helper<Container,DerivedPolicies,boost::python::detail::no_proxy_helper<Container,DerivedPolicies,boost::python::detail::container_element<Container,Index,DerivedPolicies>,Index>,Data,Index>' being compiled  
1>        with  
1>        [  
1>            Container=std::vector<double,std::allocator<double>>,  
1>            DerivedPolicies=boost::python::detail::final_vector_derived_policies<std::vector<double,std::allocator<double>>,false>,  
1>            Index=unsigned __int64,  
1>            Data=double  
1>        ]  
1>C:\ThirdParty\1.8.0\win64-vc14\boost\include\boost-1_63\boost/python/suite/indexing/indexing_suite.hpp(253): note: while compiling class template member function 'void boost::python::indexing_suite<Container,DerivedPolicies,false,false,double,unsigned __int64,double>::base_delete_item(Container &,PyObject *)'  
1>        with  
1>        [  
1>            Container=std::vector<double,std::allocator<double>>,  
1>            DerivedPolicies=boost::python::detail::final_vector_derived_policies<std::vector<double,std::allocator<double>>,false>  
1>        ]  
1>C:\ThirdParty\1.8.0\win64-vc14\boost\include\boost-1_63\boost/python/suite/indexing/indexing_suite.hpp(186): note: see reference to function template instantiation 'void boost::python::indexing_suite<Container,DerivedPolicies,false,false,double,unsigned __int64,double>::base_delete_item(Container &,PyObject *)' being compiled  
1>        with  
1>        [  
1>            Container=std::vector<double,std::allocator<double>>,  
1>            DerivedPolicies=boost::python::detail::final_vector_derived_policies<std::vector<double,std::allocator<double>>,false>  
1>        ]  
1>C:\ThirdParty\1.8.0\win64-vc14\boost\include\boost-1_63\boost/python/suite/indexing/vector_indexing_suite.hpp(50): note: see reference to class template instantiation 'boost::python::indexing_suite<Container,DerivedPolicies,false,false,double,unsigned __int64,double>' being compiled  
1>        with  
1>        [  
1>            Container=std::vector<double,std::allocator<double>>,  
1>            DerivedPolicies=boost::python::detail::final_vector_derived_policies<std::vector<double,std::allocator<double>>,false>  
1>        ]  
1>C:\ThirdParty\1.8.0\win64-vc14\boost\include\boost-1_63\boost/python/suite/indexing/detail/indexing_suite_detail.hpp(619): warning C4267: '+=': conversion from 'size_t' to 'long', possible loss of data  
```  
  
I'm using Boost 1.63, VisualStudio 2017 15.5.4.

---

## Comment 1

> Username: vai-brma  
> Created at: 2021-05-03 18:27:37 UTC  
> Url: https://github.com/boostorg/python/issues/184#issuecomment-831445653  

This is still a problem in 1.75.0  
indexing_suite_detail.hpp   
                long from = extract<long>( slice->start);  
                if (from < 0) // Negative slice index  
                    from += max_index;
