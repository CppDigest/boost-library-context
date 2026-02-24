# #424 - autodiff::fvar doesn't work with Eigen [Open]

> Username: wgledbetter  
> Created at: 2020-08-25 23:58:10 UTC  
> Updated at: 2020-08-26 16:22:16 UTC  
> Url: https://github.com/boostorg/math/issues/424  

Currently, the templated copy constructor for `autodiff::fvar` interferes with Eigen's lazy evaluation, and, in my experience, produces two types of errors:  
1. Ambiguous operators  
- Example error: `ambiguous overload for ‘operator*’ (operand types are ‘Eigen::Transpose<Eigen::Matrix<boost::math::differentiation::autodiff_v1::detail::fvar<double, 1>, 4, 1> >’ and ‘Eigen::Matrix<boost::math::differentiation::autodiff_v1::detail::fvar<double, 1>, 4, 1>’)`  
2. Failed attempts to `static_cast` a matrix into an fvar  
- Example error: `error: invalid ‘static_cast’ from type ‘const Eigen::CwiseUnaryOp<Eigen::internal::scalar_cast_op<double, boost::math::differentiation::autodiff_v1::detail::fvar<double, 1> >, const Eigen::Matrix<double, 4, 4> >’ to type ‘double’`  
  
A solution would be to only define the constructor for types that are convertible to the fvar's RealType. A colleague and I worked out a potential fix using SFINAE that I'm currently testing against the existing `test_autodiff` files. Tests 1-3 have succeeded with the existing travis configuration, but I'm having issues in test_autodiff_4 with `boost::has_right_shift` as called from `boost::lexical_cast`.  
  
Is there any interest in merging a solution to this problem? I can post the work I've done so far.

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-08-26 00:17:26 UTC  
> Url: https://github.com/boostorg/math/issues/424#issuecomment-680341737  

@wgledbetter : Yes, we're interested.  
  
@pulver : Can your review the PR when it shows up?

---

## Comment 2

> Username: pulver  
> Created at: 2020-08-26 12:20:58 UTC  
> Updated at: 2020-08-26 12:52:43 UTC  
> Url: https://github.com/boostorg/math/issues/424#issuecomment-680845213  

The build errors @wgledbetter forewarned of are on https://travis-ci.org/github/boostorg/math/builds/721216811  
  
```  
../../../boost/static_assert.hpp:31:45: error: static assertion failed: Target type is neither std::istream`able nor std::wistream`able  
 #     define BOOST_STATIC_ASSERT_MSG( ... ) static_assert(__VA_ARGS__)  
                                             ^  
../../../boost/lexical_cast/detail/converter_lexical.hpp:243:13: note: in expansion of macro ‘BOOST_STATIC_ASSERT_MSG’  
             BOOST_STATIC_ASSERT_MSG((result_t::value || boost::has_right_shift<std::basic_istream<wchar_t>, T >::value),  
             ^~~~~~~~~~~~~~~~~~~~~~~  
```  
In case this helps someone diagnose this, the larger context in `boost/lexical_cast/detail/converter_lexical.hpp` where this is called is:  
```  
    namespace detail  // deduce_target_char_impl<T>  
    {  
        // If type T is `deduce_character_type_later` type, then tries to deduce  
        // character type using boost::has_right_shift<T> metafunction.  
        // Otherwise supplied type T is a character type, that must be normalized  
        // using normalize_single_byte_char<Char>.  
        // Executed at Stage 2  (See deduce_source_char<T> and deduce_target_char<T>)  
        template < class Char >  
        struct deduce_target_char_impl  
        {  
            typedef BOOST_DEDUCED_TYPENAME normalize_single_byte_char< Char >::type type;  
        };  
  
        template < class T >  
        struct deduce_target_char_impl< deduce_character_type_later<T> >  
        {  
            typedef boost::has_right_shift<std::basic_istream<char>, T > result_t;  
  
#if defined(BOOST_LCAST_NO_WCHAR_T)  
            BOOST_STATIC_ASSERT_MSG((result_t::value),  
                "Target type is not std::istream`able and std::wistream`s are not supported by your STL implementation");  
            typedef char type;  
#else  
            typedef BOOST_DEDUCED_TYPENAME boost::conditional<  
                result_t::value, char, wchar_t  
            >::type type;  
  
            BOOST_STATIC_ASSERT_MSG((result_t::value || boost::has_right_shift<std::basic_istream<wchar_t>, T >::value),   
                "Target type is neither std::istream`able nor std::wistream`able");  
#endif  
        };  
    }  
```  
This appears to be called when the RealType is a multiprecision:  
`../../../boost/lexical_cast/detail/converter_lexical.hpp: In instantiation of ‘struct boost::detail::deduce_target_char_impl<boost::detail::deduce_character_type_later<boost::math::differentiation::autodiff_v1::detail::fvar<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<50u> >, 5ul> > >’`

---

## Comment 3

> Username: wgledbetter  
> Created at: 2020-08-26 16:22:15 UTC  
> Url: https://github.com/boostorg/math/issues/424#issuecomment-680983242  

I was able to solve this problem by creating an `operator>>` for fvar that behaves like `operator=`. Now I'm getting all autodiff tests to pass.  
  
Just a side note, I got this same has_right_shift error with RealType = double on my personal machine with clang-10 and gcc-10 and c++-17, but the new update on #425 fixes it, too.
