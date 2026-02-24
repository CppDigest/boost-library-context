# #41 - -Wunused-variable on BOOST_MPL_ASSERT for clang-cl [Closed]

> Username: JVApen  
> Created at: 2019-03-23 14:48:03 UTC  
> Updated at: 2022-05-01 21:23:45 UTC  
> Closed at: 2022-05-01 21:23:45 UTC  
> Url: https://github.com/boostorg/mpl/issues/41  

I've noticed the following issue when enabling -Wunused-variable on a Window project using clang-cl.  
  
1>boost/mpl/for_each.hpp(99,5): error : unused variable 'mpl_assertion_in_line_99' [-Werror,-Wunused-variable]  
1>    BOOST_MPL_ASSERT(( is_sequence<Sequence> ));  
1>    ^  
1>boost/mpl/assert.hpp(287,7):  note: expanded from macro 'BOOST_MPL_ASSERT'  
1>    , BOOST_PP_CAT(mpl_assertion_in_line_,BOOST_MPL_AUX_PP_COUNTER()) = sizeof( \  
1>      ^  
1>boost/preprocessor/cat.hpp(22,32):  note: expanded from macro 'BOOST_PP_CAT'  
1>boost/preprocessor/cat.hpp(29,34):  note: expanded from macro 'BOOST_PP_CAT_I'  
1><scratch space>(105,1):  note: expanded from here  
1>private_code.cpp:  note: in instantiation of function template specialization 'boost::mpl::for_each<boost::mpl::set<Enum::A, Enum::B, Enum::C, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na, mpl_::na>, (anonymous namespace)::Function<wchar_t *> >' requested here  
1>   mpl::for_each<Enum>(tuple);  
1>        ^  
  
I've tried to reproduce on compiler explorer, however, clang-cl ain't available.  
Would it be possible to add [[maybe_unused]] when supported, to the line 280 of assert.hpp?

---

## Comment 1

> Username: eldiener  
> Created at: 2020-09-25 19:56:46 UTC  
> Url: https://github.com/boostorg/mpl/issues/41#issuecomment-699125512  

Could you please post some code that can reproduce your problem. Running the mpl tests with clang on Windows with cxxflags=-Wunused-variable", all tests pass. I need some simple code that reproduces this problem with clang on Windows using clang-cl.
