# #411 - How is struct_macros.hpp generated? [Closed]

> Username: danielchen0  
> Created at: 2018-06-26 17:05:05 UTC  
> Updated at: 2018-06-28 22:46:14 UTC  
> Closed at: 2018-06-26 17:24:34 UTC  
> Url: https://github.com/boostorg/hana/issues/411  

I noticed that in the struct_macros.hpp header file, which defines BOOST_HANA_ADAPT_STRUCT and BOOST_HANA_DEFINE_STRUCT, there is a lot of hardcoding to implement these macros for a variadic number of parameters:  
  
	#define BOOST_HANA_ADAPT_STRUCT_IMPL_1(TYPE )    \  
	    namespace boost { namespace hana {                                                            \  
	        template <>                                                                               \  
	        struct accessors_impl<TYPE> {                                                             \  
	            static constexpr auto apply() {                                                       \  
	                struct member_names {                                                             \  
	                  static constexpr auto get() {                                                   \  
	                      return ::boost::hana::make_tuple(                                           \  
	                             \  
	                      );                                                                          \  
	                  }                                                                               \  
	                };                                                                                \  
	                return ::boost::hana::make_tuple(                                                 \  
	                    \  
	                );                                                                                \  
	            }                                                                                     \  
	        };                                                                                        \  
	    }}                                                                                            \  
	/**/  
	  
	#define BOOST_HANA_ADAPT_STRUCT_IMPL_2(TYPE , m1)    \  
	    namespace boost { namespace hana {                                                            \  
	        template <>                                                                               \  
	        struct accessors_impl<TYPE> {                                                             \  
	            static constexpr auto apply() {                                                       \  
	                struct member_names {                                                             \  
	                  static constexpr auto get() {                                                   \  
	                      return ::boost::hana::make_tuple(                                           \  
	                          BOOST_HANA_PP_STRINGIZE(m1)   \  
	                      );                                                                          \  
	                  }                                                                               \  
	                };                                                                                \  
	                return ::boost::hana::make_tuple(                                                 \  
	                    ::boost::hana::make_pair(::boost::hana::struct_detail::prepare_member_name<0, member_names>(), ::boost::hana::struct_detail::member_ptr<decltype(&TYPE::m1), &TYPE::m1>{})\  
	                );                                                                                \  
	            }                                                                                     \  
	        };                                                                                        \  
	    }}                                                                                            \  
	/**/  
	  
	#define BOOST_HANA_ADAPT_STRUCT_IMPL_3(TYPE , m1, m2)    \  
	    namespace boost { namespace hana {                                                            \  
	        template <>                                                                               \  
	        struct accessors_impl<TYPE> {                                                             \  
	            static constexpr auto apply() {                                                       \  
	                struct member_names {                                                             \  
	                  static constexpr auto get() {                                                   \  
	                      return ::boost::hana::make_tuple(                                           \  
	                          BOOST_HANA_PP_STRINGIZE(m1), BOOST_HANA_PP_STRINGIZE(m2)   \  
	                      );                                                                          \  
	                  }                                                                               \  
	                };                                                                                \  
	                return ::boost::hana::make_tuple(                                                 \  
	                    ::boost::hana::make_pair(::boost::hana::struct_detail::prepare_member_name<0, member_names>(), ::boost::hana::struct_detail::member_ptr<decltype(&TYPE::m1), &TYPE::m1>{}), ::boost::hana::make_pair(::boost::hana::struct_detail::prepare_member_name<1, member_names>(), ::boost::hana::struct_detail::member_ptr<decltype(&TYPE::m2), &TYPE::m2>{})\  
	                );                                                                                \  
	            }                                                                                     \  
	        };                                                                                        \  
	    }}                                                                                            \  
	/**/  
  
  
This seems error-prone to do this by hand, so I am wondering how this file (and similar) is maintained or generated. Are you running a script somewhere to create these?

---

## Comment 1

> Username: ricejasonf  
> Created at: 2018-06-26 17:20:41 UTC  
> Url: https://github.com/boostorg/hana/issues/411#issuecomment-400395525  

It says at the top of that file:  
  
```cpp  
//////////////////////////////////////////////////////////////////////////////  
// THIS FILE IS GENERATED FROM THE <boost/hana/detail/struct_macros.erb.hpp>  
// ERB TEMPLATE. DO NOT EDIT THIS FILE DIRECTLY.  
//  
// THE ERB TEMPLATE CONTAINS INFORMATION ABOUT HOW TO REGENERATE THIS FILE.  
//////////////////////////////////////////////////////////////////////////////  
```  
  
It is an eRuby template which is also what Metabench uses.  
  
https://github.com/ldionne/metabench  
  
https://github.com/boostorg/hana/blob/master/include/boost/hana/detail/struct_macros.hpp.erb

---

## Comment 2

> Username: danielchen0  
> Created at: 2018-06-26 17:24:32 UTC  
> Url: https://github.com/boostorg/hana/issues/411#issuecomment-400396715  

Ah, I didn't see that somehow. Thanks!

---

## Comment 3

> Username: danielchen0  
> Created at: 2018-06-26 19:02:26 UTC  
> Url: https://github.com/boostorg/hana/issues/411#issuecomment-400426995  

Are you using the [erb](http://ruby-doc.org/stdlib-2.5.1/libdoc/erb/rdoc/ERB.html) implementation of eRuby?

---

## Comment 4

> Username: ricejasonf  
> Created at: 2018-06-26 19:19:42 UTC  
> Url: https://github.com/boostorg/hana/issues/411#issuecomment-400431702  

^ @ldionne

---

## Comment 5

> Username: ldionne  
> Created at: 2018-06-28 22:46:14 UTC  
> Url: https://github.com/boostorg/hana/issues/411#issuecomment-401195580  

@d223chen Yes, I'm using the default eRuby implementation provided with Ruby. If you look at https://github.com/boostorg/hana/blob/master/include/boost/hana/detail/struct_macros.hpp.erb, a comment at the top explains how to generate the file:  
  
```  
export MAX_NUMBER_OF_MEMBERS=55; erb struct_macros.hpp.erb  
```
