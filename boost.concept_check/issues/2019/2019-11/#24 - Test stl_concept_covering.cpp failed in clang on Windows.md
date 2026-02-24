# #24 - Test stl_concept_covering.cpp failed in clang on Windows [Closed]

> Username: yuxianch  
> Created at: 2019-11-25 13:58:45 UTC  
> Updated at: 2022-04-24 12:38:17 UTC  
> Closed at: 2022-04-24 12:38:17 UTC  
> Url: https://github.com/boostorg/concept_check/issues/24  

When running latest stl_concept_covering.cpp in clang on Windows, I got the following error message.  
```  
In file included from stl_concept_covering.cpp:6:  
vs2019/VC/Tools/MSVC/14.20.27508/include\algorithm(2467,9): error: cannot decrement value of type 'boost::mutable_forward_iterator_archetype<boost::sgi_assignable_archetype<boost::convertible_to_archetype<boost::null_archetype<int>, boost::default_archetype_base> > >'  
        --_Last;  
        ^ ~~~~~  
vs2019/VC/Tools/MSVC/14.20.27508/include\algorithm(2488,27): note: in instantiation of function template specialization 'std::_Stable_partition_unchecked<boost::mutable_forward_iterator_archetype<boost::sgi_assignable_archetype<boost::convertible_to_archetype<boost::null_archetype<int>, boost::default_archetype_base> > >, boost::unary_predicate_archetype<boost::null_archetyp  
e<int> > >' requested here  
    _Seek_wrapped(_First, _Stable_partition_unchecked(_Get_unwrapped(_First), _Get_unwrapped(_Last), _Pass_fn(_Pred)));  
                          ^  
stl_concept_covering.cpp(558,15): note: in instantiation of function template specialization 'std::stable_partition<boost::mutable_forward_iterator_archetype<boost::sgi_assignable_archetype<boost::convertible_to_archetype<boost::null_archetype<int>, boost::default_archetype_base> > >, boost::unary_predicate_archetype<boost::null_archetype<int> > >' requested here  
    fi = std::stable_partition(fi, fi, pred);  
              ^  
```  
I noticed these few lines would not run in msvc.  
```  
ksh-3.2$ sed -n "552,561p" stl_concept_covering.cpp  
#ifndef BOOST_MSVC  
  {  
    // fails on MSVC  
    typedef null_archetype<> PredArg;  
    typedef sgi_assignable_archetype<convertible_to_archetype<PredArg> > FT;  
    mutable_forward_iterator_archetype<FT> fi;  
    unary_predicate_archetype<PredArg> pred(dummy_cons);  
    fi = std::stable_partition(fi, fi, pred);  
  }  
#endif   
```  
If this test was forced to run in msvc(cl), and there would be an similar error in line 558 too. The error message of cl looks like below.  
```  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.15.26726\include\algorithm(2888): error C2675: unary '--': '_BidIt' does not define this operator or a conversion to a type acceptable to the predefined operator  
        with  
        [  
            _BidIt=boost::mutable_forward_iterator_archetype<FT>  
        ]  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.15.26726\include\algorithm(2914): note: see reference to function template instantiation '_BidIt std::_Stable_partition_unchecked<_Iter,_Fn>(_BidIt,_BidIt,_Pr)' being compiled  
        with  
        [  
            _BidIt=boost::mutable_forward_iterator_archetype<FT>,  
            _Iter=boost::mutable_forward_iterator_archetype<FT>,  
            _Fn=boost::unary_predicate_archetype<T>,  
            _Pr=boost::unary_predicate_archetype<T>  
        ]  
stl_concept_covering.cpp(558): note: see reference to function template instantiation '_BidIt std::stable_partition<boost::mutable_forward_iterator_archetype<FT>,boost::unary_predicate_archetype<T>>(_BidIt,_BidIt,_Pr)' being compiled  
        with  
        [  
            _BidIt=boost::mutable_forward_iterator_archetype<FT>,  
            _Pr=boost::unary_predicate_archetype<T>  
        ]  
```  
So, if this is an known issue on Windows and cannot be fixed,   
1. should the macro judgement in this code segment be changed to avoid running in some other compilers on Windows, like clang-cl?  
2. is it possible to make some changes in this code to avoid errors on Windows? Still no clear why it can run on Linux but not on Windows.  
Thanks a lot!

---

## Comment 1

> Username: yuxianch  
> Created at: 2020-07-03 03:31:53 UTC  
> Url: https://github.com/boostorg/concept_check/issues/24#issuecomment-653322146  

This issue can be fixed by replacing "#ifndef  BOOST_MSVC" with "#ifndef  _MSC_VER".
