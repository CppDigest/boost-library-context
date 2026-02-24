# #415 - Compilation errors with VS 2017 15.3 and /permissive- [Open]

> Username: mwu-tow  
> Created at: 2017-08-16 00:13:54 UTC  
> Updated at: 2017-10-05 13:20:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/415  

I have installed Visual Studio 2017 with latest updates (15.3) and Boost 1.65 RC3.  
  
I am trying to use the `/permissive-` compiler switch that is supposed to make VS stick more closely to the C++ standard. However when I enable that switch I get the compilation errors from Boost.Geometry headers.  
  
For the sample program   
```  
#define _HAS_AUTO_PTR_ETC 1  
  
#include <boost/geometry.hpp>  
  
int main(int, char*[])  
{  
}  
```  
  
I get the errors:  
```  
Unknown compiler version - please run the configure tests and report the results  
c:\c++\libs-v140\boost_1_65_0\boost\geometry\algorithms\assign.hpp(236,1): error C2760: syntax error: expected ';' not '('  
        BOOST_MPL_ASSERT_MSG  
^  
c:\c++\libs-v140\boost_1_65_0\boost\geometry\algorithms\assign.hpp(253): note: see reference to class template instantiation 'boost::geometry::resolve_variant::assign<Geometry1,Geometry2>' being compiled  
};  
c:\c++\libs-v140\boost_1_65_0\boost\geometry\iterators\concatenate_iterator.hpp(86,1): error C2760: syntax error: expected ';' not '('  
        BOOST_MPL_ASSERT_MSG((are_conv),  
^  
c:\c++\libs-v140\boost_1_65_0\boost\geometry\iterators\concatenate_iterator.hpp(147): note: see reference to class template instantiation 'boost::geometry::concatenate_iterator<Iterator1,Iterator2,Value,Reference>' being compiled  
};  
c:\c++\libs-v140\boost_1_65_0\boost\geometry\iterators\flatten_iterator.hpp(105,1): error C2760: syntax error: expected ';' not '('  
        BOOST_MPL_ASSERT_MSG((are_conv),  
^  
c:\c++\libs-v140\boost_1_65_0\boost\geometry\iterators\flatten_iterator.hpp(222): note: see reference to class template instantiation 'boost::geometry::flatten_iterator<OuterIterator,InnerIterator,Value,AccessInnerBegin,AccessInnerEnd,Reference>' being compiled  
};  
c:\c++\libs-v140\boost_1_65_0\boost\geometry\iterators\detail\segment_iterator\range_segment_iterator.hpp(156,1): error C2760: syntax error: expected ';' not '('  
        BOOST_MPL_ASSERT_MSG((are_conv), NOT_CONVERTIBLE, (types<OtherRange>));  
^  
c:\c++\libs-v140\boost_1_65_0\boost\geometry\iterators\detail\segment_iterator\range_segment_iterator.hpp(206): note: see reference to class template instantiation 'boost::geometry::detail::segment_iterator::range_segment_iterator<Range,Value,Reference>' being compiled  
};  
c:\c++\libs-v140\boost_1_65_0\boost\geometry\algorithms\is_convex.hpp(165,1): fatal error C1903: unable to recover from previous error(s); stopping compilation  
    static bool apply(Geometry const& geometry, geometry::default_strategy const&)  
^  
```  
  
It seems to be related to the usage of `BOOST_MPL_ASSERT_MSG` of macro — though I am not sure if the problem comes from the macro itself or its usage within Boost.Geometry.

---

## Comment 1

> Username: awulkiew  
> Created at: 2017-08-16 12:11:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/415#issuecomment-322750970  

Thanks for the report.  
  
The first line `Unknown compiler version - please run the configure tests and report the results` suggests that this may be related to Boost.Config. If you wanted to check this and report at Boost.Config you could run the tests like this: from `BOOST_ROOT` call `b2 toolset=msvc-15.3 cxxflags="/permissive-" libs/config/test`.  
  
Before every `BOOST_MPL_ASSERT_MSG` mentioned in the compiler output there is a similar code, i.e. a `static const bool` variable is initialized with some `static const` `value` or value of a compile-time expression. Is `constexpr` required in function body in such cases now? Would you be willing to do some experiments? E.g. you could try replacing `const` keyword with `constexpr` keyword or `static bool const` with `BOOST_STATIC_CONSTEXPR bool`? The latter may not work if Boost.Config is not configured properly.

---

## Comment 2

> Username: mwu-tow  
> Created at: 2017-08-16 12:31:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/415#issuecomment-322755560  

Thank you for the answer. As for the unknown compiler — it is caused by the latest VS update. The toolset was upgraded and `_MSC_VER` was incremented to 1911. I don't think that it is relevant for this issue. If I take down the `/permissive-` switch, the program compiles file despite the unknown compiler version warning.  
  
The Boost.Config was already notified of the issue, see https://github.com/boostorg/config/issues/176.  
  
Using `constexpr` or `BOOST_STATIC_CONSTEXPR` doesn't change anything. It seems that line before macro is good, the error comes from macro usage.  
  
Let's consider the first error coming from:  
```  
template <typename Geometry1, typename Geometry2>  
struct assign  
{  
    static inline void  
    apply(Geometry1& geometry1, const Geometry2& geometry2)  
    {  
        concepts::check<Geometry1>();  
        concepts::check<Geometry2 const>();  
        concepts::check_concepts_and_equal_dimensions<Geometry1, Geometry2 const>();  
              
        static bool const same_point_order  
            = point_order<Geometry1>::value == point_order<Geometry2>::value;  
        BOOST_MPL_ASSERT_MSG  
        (  
            (same_point_order),  
            ASSIGN_IS_NOT_SUPPORTED_FOR_DIFFERENT_POINT_ORDER,  
            (types<Geometry1, Geometry2>)  
        );  
        static bool const same_closure  
            = closure<Geometry1>::value == closure<Geometry2>::value;  
        BOOST_MPL_ASSERT_MSG  
        (  
            (same_closure),  
            ASSIGN_IS_NOT_SUPPORTED_FOR_DIFFERENT_CLOSURE,  
            (types<Geometry1, Geometry2>)  
        );  
              
        dispatch::convert<Geometry2, Geometry1>::apply(geometry2, geometry1);  
    }  
};  
```  
  
It gets preprocessed to:  
```  
template <typename Geometry1, typename Geometry2>  
struct assign  
{  
    static inline void  
    apply(Geometry1& geometry1, const Geometry2& geometry2)  
    {  
        concepts::check<Geometry1>();  
        concepts::check<Geometry2 const>();  
        concepts::check_concepts_and_equal_dimensions<Geometry1, Geometry2 const>();  
              
        static bool const same_point_order  
            = point_order<Geometry1>::value == point_order<Geometry2>::value;  
        struct ASSIGN_IS_NOT_SUPPORTED_FOR_DIFFERENT_POINT_ORDER; typedef struct ASSIGN_IS_NOT_SUPPORTED_FOR_DIFFERENT_POINT_ORDER54 : boost::mpl::assert_ { static boost::mpl::failed ************ (ASSIGN_IS_NOT_SUPPORTED_FOR_DIFFERENT_POINT_ORDER::************ assert_arg()) (types<Geometry1, Geometry2>) { return 0; } } mpl_assert_arg54; static const std::size_t mpl_assertion_in_line_54 = sizeof( boost::mpl::assertion_failed<((same_point_order))>( mpl_assert_arg54::assert_arg() ) );  
#line 242 "c:\\c++\\libs-v140\\boost_1_65_0\\boost\\geometry\\algorithms\\assign.hpp"  
        static bool const same_closure  
            = closure<Geometry1>::value == closure<Geometry2>::value;  
        struct ASSIGN_IS_NOT_SUPPORTED_FOR_DIFFERENT_CLOSURE; typedef struct ASSIGN_IS_NOT_SUPPORTED_FOR_DIFFERENT_CLOSURE55 : boost::mpl::assert_ { static boost::mpl::failed ************ (ASSIGN_IS_NOT_SUPPORTED_FOR_DIFFERENT_CLOSURE::************ assert_arg()) (types<Geometry1, Geometry2>) { return 0; } } mpl_assert_arg55; static const std::size_t mpl_assertion_in_line_55 = sizeof( boost::mpl::assertion_failed<((same_closure))>( mpl_assert_arg55::assert_arg() ) );  
#line 250 "c:\\c++\\libs-v140\\boost_1_65_0\\boost\\geometry\\algorithms\\assign.hpp"  
              
        dispatch::convert<Geometry2, Geometry1>::apply(geometry2, geometry1);  
    }  
};  
```  
  
That likely exceeds my knowledge of C++ dark corners, but is `static boost::mpl::failed ************ (ASSIGN_IS_NOT_SUPPORTED_FOR_DIFFERENT_POINT_ORDER::************ assert_arg()) (types<Geometry1, Geometry2>) ` a valid function singature? It seems to be the thing causing the error.

---

## Comment 3

> Username: mwu-tow  
> Created at: 2017-08-16 12:55:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/415#issuecomment-322761485  

On the other hand it is not that all `BOOST_MPL_ASSERT_MSG` usage will fail. I just tried the one from the documenatation sample (http://www.boost.org/doc/libs/1_49_0/libs/mpl/doc/refmanual/assert-msg.html) and it works as expected.  
  
```  
#define _HAS_AUTO_PTR_ETC 1  
  
#include <boost/mpl/assert.hpp>  
#include <boost/type_traits.hpp>  
  
template< typename T > struct my  
{  
	// ...  
	BOOST_MPL_ASSERT_MSG(  
		boost::is_integral<T>::value  
		, NON_INTEGRAL_TYPES_ARE_NOT_ALLOWED  
		, (T)  
	);  
};  
  
//my<void*> test;  
  
int main(int, char*[])  
{  
}  
```

---

## Comment 4

> Username: awulkiew  
> Created at: 2017-08-16 13:05:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/415#issuecomment-322764366  

Yes, I think it's a static function taking variable of type `types<...>` returning a pointer to pointer to (...) to static member function returning pointer to pointer to (...) to `boost::mpl::failed`.  
  
> On the other hand it is not that all BOOST_MPL_ASSERT_MSG usage will fail.  
  
You mean when `my` template is not instantiated it doesn't fail (it's commented out)?  
  
Note that in the case of Geometry `BOOST_MPL_ASSERT_MSG` is used in a function body (of a static function of a struct template).

---

## Comment 5

> Username: mwu-tow  
> Created at: 2017-08-16 13:13:29 UTC  
> Updated at: 2017-08-16 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/415#issuecomment-322766592  

> You mean when my template is not instantiated it doesn't fail (it's commented out)?  
  
No, it will succeed even if instantiated, if assert condition is fulfilled. So it seems that there is something in the specific usage in the Geometry library that triggers the errors.  
  
> Note that in the case of Geometry BOOST_MPL_ASSERT_MSG is used in a function body (of a static function of a struct template).  
  
It is not enough to trigger the issue. The below compiles fine:  
  
```  
#define _HAS_AUTO_PTR_ETC 1  
  
#include <boost/mpl/assert.hpp>  
#include <boost/type_traits.hpp>  
  
template< typename T > struct my  
{  
	static inline void foo()  
	{  
		BOOST_MPL_ASSERT_MSG(  
			boost::is_integral<T>::value  
			, NON_INTEGRAL_TYPES_ARE_NOT_ALLOWED  
			, (T)  
		);  
	}  
};  
  
my<int> test;  
// my<void*> test2;   
```  
  
I will try to reduce this further, by going from the sample above to the case from the library.

---

## Comment 6

> Username: mwu-tow  
> Created at: 2017-08-16 13:32:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/415#issuecomment-322771962  

I think I got it. There are basically two conditions needed to trigger the issue:  
1) `BOOST_MPL_ASSERT_MSG` must be placed in a method definition  
2) third macro argument must be `types<T1, T2>` (with at least two types given)  
I was able to workaround the problem in Boost.Geometry by replacing `(types<Geometry1, Geometry2>)` with `(Geometry1, Geometry2)` etc.  
  
Is there any reason for preferring `(types<T1,T2>)` over `(T1,T2)`? The MPL documentation page describes both forms that should be equivalent if types are non-`void` which I believe must be a case here.  
  
Still not sure if it is a bug in Boost.MPL or Visual C++.  
  
My repro case:   
```  
#define _HAS_AUTO_PTR_ETC 1  
  
#include <boost/mpl/assert.hpp>  
#include <boost/type_traits.hpp>  
  
template<typename T1, typename T2> struct my  
{  
	static void foo()  
	{  
		BOOST_MPL_ASSERT_MSG(  
			(boost::is_same<T1, T2>::value)  
			, MUST_BE_THE_SAME  
			, (types<T1, T2>) // fails  
			//, (T1, T2)  // does work  
		);  
	}  
};  
  
int main(int, char*[])  
{  
	my<int, int>{}.foo();  
}  
```

---

## Comment 7

> Username: mwu-tow  
> Created at: 2017-08-16 14:09:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/415#issuecomment-322783436  

After further reducing I believe that this is an error in Visual C++ compiler, see https://developercommunity.visualstudio.com/content/problem/95255/cannot-compile-valid-c-file-with-permissive.html  
  
The reduced code:  
```  
namespace base  
{  
	struct Base  
	{  
		struct base_type {};  
	};  
}  
  
template <typename T1> struct Foo   
{  
	static void foo()  
	{  
		struct Bar : base::Base  
		{  
			void method(base_type) // the base_type is not visible here, even though shuld be inherited from Base  
			{}  
		};  
	}  
};  
  
int main(int, char*[])  
{  
	Foo<int>{}.foo();  
}  
```  
  
I'll look for a possible workaround on the MPL side.

---

## Comment 8

> Username: awulkiew  
> Created at: 2017-08-18 23:42:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/415#issuecomment-323483706  

Thanks for your investigation!  
  
> Is there any reason for preferring (types<T1,T2>) over (T1,T2)? The MPL documentation page describes both forms that should be equivalent if types are non-void which I believe must be a case here.  
  
To be honest I'm not sure. It simply may be some legacy code and therefore could be changed.  
  
Another way of dealing with this could be the replacement of `BOOST_MPL_ASSERT_MSG` with C++17 version of static_assert() with message. Assuming it was possible to generate some nice compile-time string automatically from types passed into the macro. Unless we decided to remove the info about types from the message. In many cases it's not helpful anyway but the message would become more clear.

---

## Comment 9

> Username: mwu-tow  
> Created at: 2017-08-19 14:17:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/415#issuecomment-323525790  

`static_assert` with message is available since C++11, it is the version without message that was added with C++17. But, as you say, it has the downside that type information is not as clearly provided, as with MPL's assert.   
  
If you care about fixing this issue, then I suggest either using (T1,T2) or fully qualifying the types (ie `boost::mpl::types<…>`) — both fix the issue for me, while preserving the diagnostics quality. I failed to found elegant solution on the MPL side and knowing that it is a bug in MS compiler that is already internally fixed I'm not really invested into taking any further actions.

---

## Comment 10

> Username: awulkiew  
> Created at: 2017-10-05 13:02:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/415#issuecomment-334457935  

Ok, thanks! I'll leave this issue open as a reminder for me. I'll investigate why `types<>` were used in the past and if they are interchangeable with parenthized lists of types I'll replace them everywhere.

---

## Comment 11

> Username: awulkiew  
> Created at: 2017-10-05 13:20:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/415#issuecomment-334462374  

The requirement for parenthized list of types is that all of the types has to be non-void.  
http://www.boost.org/doc/libs/1_65_1/libs/mpl/doc/refmanual/assert-msg.html  
so they are not interchangeable and therefore the fix in each part of the code should depend on what is passed into the macro.
