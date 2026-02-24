# #67 - `replace_all` in VC++ 7.1 doesn't work from 1.55 onwards in some circumstances. [Closed]

> Username: billw2012  
> Created at: 2019-12-15 16:35:54 UTC  
> Updated at: 2023-07-06 04:46:20 UTC  
> Closed at: 2023-07-06 04:46:20 UTC  
> Url: https://github.com/boostorg/algorithm/issues/67  

Example code below, errors below that. Hopefully this isn't user error, I'm trying to upgrade some existing code, but I am stuck with this compiler.  
  
The code compiles up to and including 1.55, and, if I uncomment the commented parts of the code, it will compile again from 1.58 onwards.  
  
Compile command: `cl.exe test.cpp /Fo"test.obj" /GR /MD /Gy /W3 /EHsc /Gd /Gm- /arch:SSE2 /Zm200 /I"Microsoft Visual C++ Toolkit 2003/include" /nologo /Z7 /O2 /Oy /Oi /G7 /c /I"boost_1_55_0"`  
  
### Code  
```cpp  
#include <boost/algorithm/string.hpp>  
  
void test()  
{  
	//typedef boost::range_iterator<std::string, void>::type itr;  
  
	//itr begin, end;  
	//typedef boost::iterator_range< boost::range_iterator<std::string, void>::type > result_type;  
  
	//result_type range(begin, end);  
  
	std::string vec, search, format;  
	boost::replace_all(vec, search, format);  
}  
```  
  
### Output from 1.72  
```  
test.cpp  
boost_1_72_0\boost\range\mutable_iterator.hpp(37) : error C2146: syntax error : missing ';' before identifier 'type'  
        boost_1_72_0\boost\range\mutable_iterator.hpp(43) : see reference to class template instantiation 'boost::range_detail::extract_iterator<C>' being compiled  
        with  
        [  
            C=boost::remove_reference<boost::range_iterator<std::string,void>::type>::type  
        ]  
        boost_1_72_0\boost\range\mutable_iterator.hpp(72) : see reference to class template instantiation 'boost::range_detail::range_mutable_iterator<C>' being compiled  
        with  
        [  
            C=boost::remove_reference<boost::range_iterator<std::string,void>::type>::type  
        ]  
        boost_1_72_0\boost\range\has_range_iterator.hpp(27) : see reference to class template instantiation 'boost::range_mutable_iterator<C,Enabler>' being compiled  
        with  
        [  
            C=boost::remove_reference<boost::range_iterator<std::string,void>::type>::type,  
            Enabler=void  
        ]  
        boost_1_72_0\boost\range\has_range_iterator.hpp(27) : see reference to class template instantiation 'boost::range_detail::has_type_impl_<T>' being compiled  
        with  
        [  
            T=boost::range_mutable_iterator<boost::remove_reference<boost::range_iterator<std::string,void>::type>::type,void>  
        ]  
        boost_1_72_0\boost\mpl\eval_if.hpp(41) : see reference to class template instantiation 'boost::range_detail::has_type<T,fallback_>' being compiled  
        with  
        [  
            T=boost::range_mutable_iterator<boost::remove_reference<boost::range_iterator<std::string,void>::type>::type,void>,  
            fallback_=boost::mpl::bool_<false>  
        ]  
        boost_1_72_0\boost\range\has_range_iterator.hpp(73) : see reference to class template instantiation 'boost::mpl::eval_if<C,F1,F2>' being compiled  
        with  
        [  
            C=boost::is_const<boost::remove_reference<boost::range_iterator<std::string,void>::type>::type>,  
            F1=boost::range_detail::has_type<boost::range_const_iterator<boost::remove_reference<boost::range_iterator<std::string,void>::type>::type,void>,boost::mpl::bool_<false>>,  
            F2=boost::range_detail::has_type<boost::range_mutable_iterator<boost::remove_reference<boost::range_iterator<std::string,void>::type>::type,void>,boost::mpl::bool_<false>>  
        ]  
        boost_1_72_0\boost\range\has_range_iterator.hpp(73) : see reference to class template instantiation 'boost::range_detail::has_range_iterator_impl<T>' being compiled  
        with  
        [  
            T=boost::remove_reference<boost::range_iterator<std::string,void>::type>::type  
        ]  
        boost_1_72_0\boost\mpl\if.hpp(63) : see reference to class template instantiation 'boost::has_range_iterator<T>' being compiled  
        with  
        [  
            T=boost::range_iterator<std::string,void>::type  
        ]  
        boost_1_72_0\boost\mpl\eval_if.hpp(40) : see reference to class template instantiation 'boost::mpl::if_<T1,T2,T3>' being compiled  
        with  
        [  
            T1=boost::has_range_iterator<boost::range_iterator<std::string,void>::type>,  
            T2=boost::range_iterator<boost::range_iterator<std::string,void>::type>,  
            T3=boost::mpl::identity<void>  
        ]  
        boost_1_72_0\boost\range\iterator_range_core.hpp(452) : see reference to class template instantiation 'boost::mpl::eval_if<C,F1,F2>' being compiled  
        with  
        [  
            C=boost::has_range_iterator<boost::range_iterator<std::string,void>::type>,  
            F1=boost::range_iterator<boost::range_iterator<std::string,void>::type>,  
            F2=boost::mpl::identity<void>  
        ]  
        boost_1_72_0\boost\mpl\aux_\preprocessed\plain\and.hpp(25) : see reference to class template instantiation 'boost::iterator_range<IteratorT>::is_compatible_range_<Source>' being compiled  
        with  
        [  
            IteratorT=boost::range_iterator<std::string,void>::type,  
            Source=boost::range_iterator<std::string,void>::type  
        ]  
        boost_1_72_0\boost\mpl\aux_\preprocessed\plain\and.hpp(55) : see reference to class template instantiation 'boost::mpl::aux::and_impl<C_,T1,T2,T3,T4>' being compiled  
        with  
        [  
            C_=true,  
            T1=boost::iterator_range<boost::range_iterator<std::string,void>::type>::is_compatible_range_<boost::range_iterator<std::string,void>::type>,  
            T2=boost::mpl::true_,  
            T3=boost::mpl::true_,  
            T4=boost::mpl::true_  
        ]  
        boost_1_72_0\boost\range\iterator_range_core.hpp(469) : see reference to class template instantiation 'boost::mpl::and_<T1,T2>' being compiled  
        with  
        [  
            T1=boost::mpl::not_<boost::is_convertible<boost::range_iterator<boost::iterator_range<boost::range_iterator<std::string,void>::type>>::type,boost::iterator_range_detail::iterator_range_base<boost::range_iterator<std::string,void>::type,boost::iterators::incrementable_traversal_tag>::iterator>>,  
            T2=boost::iterator_range<boost::range_iterator<std::string,void>::type>::is_compatible_range_<boost::range_iterator<std::string,void>::type>  
        ]  
        boost_1_72_0\boost\core\enable_if.hpp(41) : see reference to class template instantiation 'boost::iterator_range<IteratorT>::is_compatible_range<Source>' being compiled  
        with  
        [  
            IteratorT=boost::range_iterator<std::string,void>::type,  
            Source=boost::range_iterator<std::string,void>::type  
        ]  
        boost_1_72_0\boost\algorithm\string\detail\finder.hpp(68) : see reference to class template instantiation 'boost::enable_if<Cond,T>' being compiled  
        with  
        [  
            Cond=boost::iterator_range<boost::range_iterator<std::string,void>::type>::is_compatible_range<boost::range_iterator<std::string,void>::type>,  
            T=void  
        ]  
        boost_1_72_0\boost\algorithm\string\find_format.hpp(272) : see reference to function template instantiation 'boost::iterator_range<IteratorT> boost::algorithm::detail::first_finderF<SearchIteratorT,PredicateT>::operator ()<boost::range_iterator<C,Enabler>::type>(ForwardIteratorT,ForwardIteratorT) const' being compiled  
        with  
        [  
            IteratorT=boost::range_iterator<std::string,void>::type,  
            SearchIteratorT=std::basic_string<char,std::char_traits<char>,std::allocator<char>>::const_iterator,  
            PredicateT=boost::algorithm::is_equal,  
            C=std::string,  
            Enabler=void,  
            ForwardIteratorT=boost::range_iterator<std::string,void>::type  
        ]  
        boost_1_72_0\boost\algorithm\string\replace.hpp(655) : see reference to function template instantiation 'void boost::algorithm::find_format_all<SequenceT,boost::algorithm::detail::first_finderF<SearchIteratorT,PredicateT>,boost::algorithm::detail::const_formatF<RangeT>>(SequenceT &,FinderT,FormatterT)' being compiled  
        with  
        [  
            SequenceT=std::string,  
            SearchIteratorT=std::basic_string<char,std::char_traits<char>,std::allocator<char>>::const_iterator,  
            PredicateT=boost::algorithm::is_equal,  
            RangeT=boost::iterator_range<boost::algorithm::detail::first_finderF<std::basic_string<char,std::char_traits<char>,std::allocator<char>>::const_iterator,boost::algorithm::is_equal>::search_iterator_type>,  
            FinderT=boost::algorithm::detail::first_finderF<std::basic_string<char,std::char_traits<char>,std::allocator<char>>::const_iterator,boost::algorithm::is_equal>,  
            FormatterT=boost::algorithm::detail::const_formatF<boost::iterator_range<boost::algorithm::detail::first_finderF<std::basic_string<char,std::char_traits<char>,std::allocator<char>>::const_iterator,boost::algorithm::is_equal>::search_iterator_type>>  
        ]  
        C:/Users\billw\Documents\GitHub\Caveman2Cosmos\boost_test\test.cpp(13) : see reference to function template instantiation 'void boost::algorithm::replace_all<std::string,std::string,std::string>(SequenceT &,const Range1T &,const Range2T &)' being compiled  
        with  
        [  
            SequenceT=std::string,  
            Range1T=std::string,  
            Range2T=std::string  
        ]  
boost_1_72_0\boost\range\mutable_iterator.hpp(37) : error C3254: 'boost::range_detail::extract_iterator<C>' : class contains explicit override '__ctor' but does not derive from an interface that contains the function declaration  
        with  
        [  
            C=boost::remove_reference<boost::range_iterator<std::string,void>::type>::type  
        ]  
boost_1_72_0\boost\range\mutable_iterator.hpp(37) : error C2838: '__ctor' : illegal qualified name in member declaration  
boost_1_72_0\boost\range\mutable_iterator.hpp(37) : error C2461: 'boost::range_detail::extract_iterator<C>' : constructor syntax missing formal parameters  
        with  
        [  
            C=boost::remove_reference<boost::range_iterator<std::string,void>::type>::type  
        ]  
boost_1_72_0\boost\range\mutable_iterator.hpp(37) : error C2501: 'boost::range_detail::extract_iterator<C>::type' : missing storage-class or type specifiers  
        with  
        [  
            C=boost::remove_reference<boost::range_iterator<std::string,void>::type>::type  
        ]  
```

---

## Comment 1

> Username: mclow  
> Created at: 2019-12-17 21:20:03 UTC  
> Url: https://github.com/boostorg/algorithm/issues/67#issuecomment-566753813  

In Boost 1.56.0, (summer of 2014) we removed a bunch of checks for very old MS compilers.   
Like this: https://github.com/boostorg/algorithm/commit/28b12d72643c3159fd61ac679920fda09a181a31
