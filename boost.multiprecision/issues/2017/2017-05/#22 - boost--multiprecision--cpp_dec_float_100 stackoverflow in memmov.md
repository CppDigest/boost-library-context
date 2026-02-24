# #22 - boost::multiprecision::cpp_dec_float_100 stackoverflow in memmov [Closed]

> Username: NAThompson  
> Created at: 2017-05-02 21:45:47 UTC  
> Updated at: 2017-06-19 14:59:44 UTC  
> Closed at: 2017-06-12 07:24:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/22  

The following code:  
  
    template<class Real>  
    void reproduce_bug()  
    {  
        Real y = 0;  
        Real z = std::numeric_limits<Real>::epsilon();  
        Real distance =  boost::math::float_distance(y, z);  
    }  
  
    int main()  
    {  
        reproduce_bug<boost::multiprecision::cpp_dec_float_100>();  
    }  
  
and compiled with:  
  
     INC = -I`pwd`/include -I../multiprecision/include  -I../..  
    CPPFLAGS = --std=c++14 -g -fsanitize=address -fsanitize=undefined  
  
    all:  
	g++ $(CPPFLAGS) $(INC) reproduce.cpp  
  
Produces the following error   
  
  
    ASAN:DEADLYSIGNAL  
    =================================================================  
    ==20801==ERROR: AddressSanitizer: stack-overflow on address 0x7ffe8ac29fc8 (pc 0x7f04de2c4649 bp 0x7ffe8ac2a850 sp 0x7ffe8ac29fd0 T0)  
     #0 0x7f04de2c4648 in memmove (/usr/lib/x86_64-linux-gnu/libasan.so.3+0x64648)  
     #1 0x563aa34395db in unsigned int* std::__copy_move<false, true, std::random_access_iterator_tag>::__copy_m<unsigned int>(unsigned int const*, unsigned int const*, unsigned int*) /usr/include/c++/6/bits/stl_algobase.h:368  
    #2 0x563aa33f8ee0 in unsigned int* std::__copy_move_a<false, unsigned int*, unsigned int*>(unsigned int*, unsigned int*, unsigned int*) /usr/include/c++/6/bits/stl_algobase.h:386  
    #3 0x563aa33c9b87 in unsigned int* std::__copy_move_a2<false, unsigned int*, unsigned int*>(unsigned int*, unsigned int*, unsigned int*) /usr/include/c++/6/bits/stl_algobase.h:424  
    #4 0x563aa339c715 in unsigned int* std::copy<unsigned int*, unsigned int*>(unsigned int*, unsigned int*, unsigned int*) /usr/include/c++/6/bits/stl_algobase.h:456  
    #5 0x563aa337402c in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::from_unsigned_long_long(unsigned long long) ../multiprecision/include/boost/multiprecision/cpp_dec_float.hpp:2345  
    #6 0x563aa3440723 in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::operator=(unsigned long long) ../multiprecision/include/boost/multiprecision/cpp_dec_float.hpp:440  
    #7 0x563aa33fbee8 in void boost::multiprecision::default_ops::detail::pow_imp<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, unsigned long long>(boost::multiprecision::backends::cpp_dec_float<100u, int, void>&, boost::multiprecision::backends::cpp_dec_float<100u, int, void> const&, unsigned long long const&, mpl_::bool_<false> const&) ../multiprecision/include/boost/multiprecision/detail/functions/pow.hpp:47  
    #8 0x563aa33cb9fc in void boost::multiprecision::default_ops::detail::pow_imp<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, long long>(boost::multiprecision::backends::cpp_dec_float<100u, int, void>&, boost::multiprecision::backends::cpp_dec_float<100u, int, void> const&, long long const&, mpl_::bool_<true> const&) ../multiprecision/include/boost/multiprecision/detail/functions/pow.hpp:85  
    #9 0x563aa339ef31 in boost::multiprecision::backends::cpp_dec_float<100u, int, void>::pow2(long long) ../multiprecision/include/boost/multiprecision/cpp_dec_float.hpp:2688  
    #10 0x563aa33fda90 in void boost::multiprecision::backends::eval_frexp<100u, int, void>(boost::multiprecision::backends::cpp_dec_float<100u, int, void>&, boost::multiprecision::backends::cpp_dec_float<100u, int, void> const&, int*) ../multiprecision/include/boost/multiprecision/cpp_dec_float.hpp:2945  
    #11 0x563aa33fddd1 in void boost::multiprecision::backends::eval_frexp<100u, int, void>(boost::multiprecision::backends::cpp_dec_float<100u, int, void>&, boost::multiprecision::backends::cpp_dec_float<100u, int, void> const&, int*) ../multiprecision/include/boost/multiprecision/cpp_dec_float.hpp:2962  
    #12 0x563aa33cc9ea in boost::enable_if_c<boost::multiprecision::number_category<boost::multiprecision::backends::cpp_dec_float<100u, int, void> >::value==((boost::multiprecision::number_category_type)1), boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> >::type boost::multiprecision::frexp<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> const&, int*) ../multiprecision/include/boost/multiprecision/detail/default_ops.hpp:2293  
    #13 0x563aa33a04c8 in boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> boost::math::detail::float_distance_imp<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> const&, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> const&, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) /home/nthompson/Documents/boost/libs/math/include/boost/math/special_functions/next.hpp:331  
    #14 0x563aa3374dda in boost::math::tools::promote_args<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>, float, float, float, float>::type boost::math::float_distance<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> const&, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> const&, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) /home/nthompson/Documents/boost/libs/math/include/boost/math/special_functions/next.hpp:390  
    #15 0x563aa332f1bf in boost::math::tools::promote_args<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>, float, float, float, float>::type boost::math::float_distance<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> >(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> const&, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> const&) /home/nthompson/Documents/boost/libs/math/include/boost/math/special_functions/next.hpp:396  
    #16 0x563aa33a0648 in boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> boost::math::detail::float_distance_imp<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> const&, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> const&, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) /home/nthompson/Documents/boost/libs/math/include/boost/math/special_functions/next.hpp:341  
    #17 0x563aa3374dda in boost::math::tools::promote_args<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>, float, float, float, float>::type boost::math::float_distance<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> const&, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> const&, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) /home/nthompson/Documents/boost/libs/math/include/boost/math/special_functions/next.hpp:390  
    #18 0x563aa332f1bf in boost::math::tools::promote_args<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>, float, float, float, float>::type boost::math::float_distance<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> >(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> const&, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> const&) /home/nthompson/Documents/boost/libs/math/include/boost/math/special_functions/next.hpp:396  
    #19 0x563aa33a0648 in boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> boost::math::detail::float_distance_imp<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> const&, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> const&, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) /home/nthompson/Documents/boost/libs/math/include/boost/math/special_functions/next.hpp:341  
    #20 0x563aa3374dda in boost::math::tools::promote_args<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>, float, float, float, float>::type boost::math::float_distance<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1>, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> const&, boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u, int, void>, (boost::multiprecision::expression_template_option)1> const&, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&) /home/nthompson/Documents/boost/libs/math/include/boost/math/special_functions/next.hpp:390  
  
Without asan, the message is simpler:  
  
    unknown location(0): fatal error: in "reproduce_bug": memory access violation at address: 0x7fff95eabff8: no mapping at fault address  
  
Version:  
  
    g++ --version  
    g++ (Ubuntu 6.3.0-12ubuntu2) 6.3.0 20170406  
  
Boost version cloned a week or so ago.  
  
Love the multiprecision library! Apologies for not submitting a patch!

---

## Comment 1

> Username: jzmaddock  
> Created at: 2017-05-03 18:46:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/22#issuecomment-299000853  

Thanks for the report, this is a known issue - float_distance does not   
currently support decimal number types and gets it's internal logic in a   
pickle.  
  
I'll try to look into this as soon as I'm able.  
  
John.  
  
  
On 02/05/2017 22:45, Nick wrote:  
>  
> The following code:  
>  
> |template<class Real> void reproduce_bug() { Real y = 0; Real z =   
> std::numeric_limits<Real>::epsilon(); Real distance =   
> boost::math::float_distance(y, z); } int main() {   
> reproduce_bug<boost::multiprecision::cpp_dec_float_100>(); } |  
>  
> and compiled with:  
>  
> |INC = -I`pwd`/include -I../multiprecision/include -I../.. CPPFLAGS =   
> --std=c++14 -g -fsanitize=address -fsanitize=undefined all: g++   
> $(CPPFLAGS) $(INC) reproduce.cpp |  
>  
> Produces the following error  
>  
> |ASAN:DEADLYSIGNAL   
> =================================================================   
> ==20801==ERROR: AddressSanitizer: stack-overflow on address   
> 0x7ffe8ac29fc8 (pc 0x7f04de2c4649 bp 0x7ffe8ac2a850 sp 0x7ffe8ac29fd0   
> T0) #0 0x7f04de2c4648 in memmove   
> (/usr/lib/x86_64-linux-gnu/libasan.so.3+0x64648) #1 0x563aa34395db in   
> unsigned int* std::__copy_move<false, true,   
> std::random_access_iterator_tag>::__copy_m<unsigned int>(unsigned int   
> const*, unsigned int const*, unsigned int*)   
> /usr/include/c++/6/bits/stl_algobase.h:368 #2 0x563aa33f8ee0 in   
> unsigned int* std::__copy_move_a<false, unsigned int*, unsigned   
> int*>(unsigned int*, unsigned int*, unsigned int*)   
> /usr/include/c++/6/bits/stl_algobase.h:386 #3 0x563aa33c9b87 in   
> unsigned int* std::__copy_move_a2<false, unsigned int*, unsigned   
> int*>(unsigned int*, unsigned int*, unsigned int*)   
> /usr/include/c++/6/bits/stl_algobase.h:424 #4 0x563aa339c715 in   
> unsigned int* std::copy<unsigned int*, unsigned int*>(unsigned int*,   
> unsigned int*, unsigned int*)   
> /usr/include/c++/6/bits/stl_algobase.h:456 #5 0x563aa337402c in   
> boost::multiprecision::backends::cpp_dec_float<100u, int,   
> void>::from_unsigned_long_long(unsigned long long)   
> ../multiprecision/include/boost/multiprecision/cpp_dec_float.hpp:2345   
> #6 0x563aa3440723 in   
> boost::multiprecision::backends::cpp_dec_float<100u, int,   
> void>::operator=(unsigned long long)   
> ../multiprecision/include/boost/multiprecision/cpp_dec_float.hpp:440   
> #7 0x563aa33fbee8 in void   
> boost::multiprecision::default_ops::detail::pow_imp<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, unsigned long   
> long>(boost::multiprecision::backends::cpp_dec_float<100u, int,   
> void>&, boost::multiprecision::backends::cpp_dec_float<100u, int,   
> void> const&, unsigned long long const&, mpl_::bool_<false> const&)   
> ../multiprecision/include/boost/multiprecision/detail/functions/pow.hpp:47   
> #8 0x563aa33cb9fc in void   
> boost::multiprecision::default_ops::detail::pow_imp<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, long   
> long>(boost::multiprecision::backends::cpp_dec_float<100u, int,   
> void>&, boost::multiprecision::backends::cpp_dec_float<100u, int,   
> void> const&, long long const&, mpl_::bool_<true> const&)   
> ../multiprecision/include/boost/multiprecision/detail/functions/pow.hpp:85   
> #9 0x563aa339ef31 in   
> boost::multiprecision::backends::cpp_dec_float<100u, int,   
> void>::pow2(long long)   
> ../multiprecision/include/boost/multiprecision/cpp_dec_float.hpp:2688   
> #10 0x563aa33fda90 in void   
> boost::multiprecision::backends::eval_frexp<100u, int,   
> void>(boost::multiprecision::backends::cpp_dec_float<100u, int,   
> void>&, boost::multiprecision::backends::cpp_dec_float<100u, int,   
> void> const&, int*)   
> ../multiprecision/include/boost/multiprecision/cpp_dec_float.hpp:2945   
> #11 0x563aa33fddd1 in void   
> boost::multiprecision::backends::eval_frexp<100u, int,   
> void>(boost::multiprecision::backends::cpp_dec_float<100u, int,   
> void>&, boost::multiprecision::backends::cpp_dec_float<100u, int,   
> void> const&, int*)   
> ../multiprecision/include/boost/multiprecision/cpp_dec_float.hpp:2962   
> #12 0x563aa33cc9ea in   
> boost::enable_if_c<boost::multiprecision::number_category<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void> >::value==((boost::multiprecision::number_category_type)1),   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> >::type   
> boost::multiprecision::frexp<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>,   
> (boost::multiprecision::expression_template_option)1>(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> const&, int*)   
> ../multiprecision/include/boost/multiprecision/detail/default_ops.hpp:2293   
> #13 0x563aa33a04c8 in   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> boost::math::detail::float_distance_imp<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>,   
> boost::math::policies::policy<boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy>   
> >(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> const&,   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> const&,   
> boost::math::policies::policy<boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy> const&)   
> /home/nthompson/Documents/boost/libs/math/include/boost/math/special_functions/next.hpp:331   
> #14 0x563aa3374dda in   
> boost::math::tools::promote_args<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>,   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>,   
> float, float, float, float>::type   
> boost::math::float_distance<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>,   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>,   
> boost::math::policies::policy<boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy>   
> >(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> const&,   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> const&,   
> boost::math::policies::policy<boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy> const&)   
> /home/nthompson/Documents/boost/libs/math/include/boost/math/special_functions/next.hpp:390   
> #15 0x563aa332f1bf in   
> boost::math::tools::promote_args<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>,   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>,   
> float, float, float, float>::type   
> boost::math::float_distance<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>,   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> >(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> const&,   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> const&)   
> /home/nthompson/Documents/boost/libs/math/include/boost/math/special_functions/next.hpp:396   
> #16 0x563aa33a0648 in   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> boost::math::detail::float_distance_imp<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>,   
> boost::math::policies::policy<boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy>   
> >(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> const&,   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> const&,   
> boost::math::policies::policy<boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy> const&)   
> /home/nthompson/Documents/boost/libs/math/include/boost/math/special_functions/next.hpp:341   
> #17 0x563aa3374dda in   
> boost::math::tools::promote_args<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>,   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>,   
> float, float, float, float>::type   
> boost::math::float_distance<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>,   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>,   
> boost::math::policies::policy<boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy>   
> >(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> const&,   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> const&,   
> boost::math::policies::policy<boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy> const&)   
> /home/nthompson/Documents/boost/libs/math/include/boost/math/special_functions/next.hpp:390   
> #18 0x563aa332f1bf in   
> boost::math::tools::promote_args<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>,   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>,   
> float, float, float, float>::type   
> boost::math::float_distance<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>,   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> >(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> const&,   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> const&)   
> /home/nthompson/Documents/boost/libs/math/include/boost/math/special_functions/next.hpp:396   
> #19 0x563aa33a0648 in   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> boost::math::detail::float_distance_imp<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>,   
> boost::math::policies::policy<boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy>   
> >(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> const&,   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> const&,   
> boost::math::policies::policy<boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy> const&)   
> /home/nthompson/Documents/boost/libs/math/include/boost/math/special_functions/next.hpp:341   
> #20 0x563aa3374dda in   
> boost::math::tools::promote_args<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>,   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>,   
> float, float, float, float>::type   
> boost::math::float_distance<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>,   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>,   
> boost::math::policies::policy<boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy>   
> >(boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> const&,   
> boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<100u,   
> int, void>, (boost::multiprecision::expression_template_option)1>   
> const&,   
> boost::math::policies::policy<boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy,   
> boost::math::policies::default_policy> const&)   
> /home/nthompson/Documents/boost/libs/math/include/boost/math/special_functions/next.hpp:390   
> |  
>  
> Without asan, the message is simpler:  
>  
> |unknown location(0): fatal error: in "numerical_differentiation_test":   
> memory access violation at address: 0x7fff95eabff8: no mapping at   
> fault address |  
>  
> Love the multiprecision library! Apologies for not submitting a patch!  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/multiprecision/issues/22>, or mute the   
> thread   
> <https://github.com/notifications/unsubscribe-auth/AEx5OBnekUEAo2fZbxSWf_aYLFISsnB-ks5r16QNgaJpZM4NOun6>.  
>  
  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 2

> Username: jzmaddock  
> Created at: 2017-05-03 18:56:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/22#issuecomment-299003262  

The initial issue is a rather intractable one: the exponent range of   
cpp_dec_float is so large, that the result of float_distance(0, eps) is   
actually a truly huge number, currently the code in float_distance uses   
recursion each time an order of magnitude boundary is crossed, since the   
space between adjacent values changes at each order of magnitude   
boundary.  The problem here, is that there are so many orders of   
magnitude to cross that the recursion will run any program out of stack   
space :(  
  
I'll try to find a non-recursive algorithm.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 3

> Username: pabristow  
> Created at: 2017-05-05 11:16:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/22#issuecomment-299439562  

I note in using float_distance to see how a series evaluation is converging that the result is an int and so, for the first term or few, using double the float_distance is often -/+ INT_MAX -2147483648, so it is not the best way of showing the float distance.  I'd like to get within a bit or two of the 'right' answer.   
  
With multiprecision types, the 'overflow' will be even more massively overflowed, and not useful information.  In practice, unless the float_distance is down to much more modest values, it isn't very meaningful.  Does this help in devising a useful algorithm in the cpp_dec_float case?  (Or am I mis-using float_distance? - should I be using epsilon_distance?)

---

## Comment 4

> Username: jzmaddock  
> Created at: 2017-06-02 18:51:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/22#issuecomment-305879489  

Initial fix here: https://github.com/boostorg/math/commit/0242c64eec85fc31ec50d2a34150daf76617ad00  
  
Handles base 2 types only at present.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2017-06-12 07:19:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/22#issuecomment-307709332  

Initial fix here: https://github.com/boostorg/math/commit/0242c64eec85fc31ec50d2a34150daf76617ad00  
  
Handles base 2 types only at present.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2017-06-12 07:24:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/22#issuecomment-307710078  

Final fixes for base 10 in https://github.com/boostorg/math/pull/68.  
  
Note that cpp_dec_float is slightly odd in that it has hidden guard digits which the code in next.hpp is unable to reason about.  As a result it normalises inputs to the nearest N-decimal digits - that gives consistent behaviour and allows all the tests to pass, but it means that something like float_next(float_prior(x)) doesn't get you back where you started - instead it gets you back to the number x would have been had cpp_dec_float performed rounding to it's stated N digits on it's last operation (as every other number type does).  
  
Hope that makes sense!

---

## Comment 7

> Username: pabristow  
> Created at: 2017-06-19 14:59:44 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/22#issuecomment-309466739  

Doesn't make sense at all!!!  But I'm sure it is the Right Thing To Do and will be useful.  I trust that this explanation is documented?
