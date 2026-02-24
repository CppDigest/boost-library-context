# #173 - error:previous definition of 'struct boost::is_unsigned< ::boost::long_long_type>:public false_type{}' g++ version 5.4.0 How can I fix it? [Closed]

> Username: GitSoftwareNow  
> Created at: 2020-09-20 07:08:02 UTC  
> Updated at: 2020-09-20 15:20:53 UTC  
> Closed at: 2020-09-20 15:20:52 UTC  
> Url: https://github.com/boostorg/date_time/issues/173  

In file included from /usr/include/boost/type_traits/make_unsigned.hpp:13:0,  
                 from /usr/include/boost/range/size_type.hpp:24,  
                 from /usr/include/boost/range/size.hpp:21,  
                 from /usr/include/boost/range/functions.hpp:20,  
                 from /usr/include/boost/range/iterator_range_core.hpp:38,  
                 from /usr/include/boost/range/iterator_range.hpp:13,  
                 from /usr/include/boost/range/adaptor/transformed.hpp:16,  
                 from /usr/include/boost/range/adaptor/map.hpp:14,  
                 from /home/kylin/桌面/hyperscan/hyperscan/hyperscan/src/nfa/mcclellancompile.cpp:63:  
/usr/include/boost/type_traits/is_signed.hpp:126:20: error: redefinition of ‘struct boost::is_signed<long long int>’  
 template <> struct is_signed<const ::boost::long_long_type> : public true_type{};  
                    ^  
/usr/include/boost/type_traits/is_signed.hpp:125:20: error: previous definition of ‘struct boost::is_signed<long long int>’  
 template <> struct is_signed< ::boost::long_long_type> : public true_type{};  
                    ^  
In file included from /usr/include/boost/type_traits/make_unsigned.hpp:14:0,  
                 from /usr/include/boost/range/size_type.hpp:24,  
                 from /usr/include/boost/range/size.hpp:21,  
                 from /usr/include/boost/range/functions.hpp:20,  
                 from /usr/include/boost/range/iterator_range_core.hpp:38,  
                 from /usr/include/boost/range/iterator_range.hpp:13,  
                 from /usr/include/boost/range/adaptor/transformed.hpp:16,  
                 from /usr/include/boost/range/adaptor/map.hpp:14,  
                 from /home/kylin/桌面/hyperscan/hyperscan/hyperscan/src/nfa/mcclellancompile.cpp:63:  
/usr/include/boost/type_traits/is_unsigned.hpp:131:20: error: redefinition of ‘struct boost::is_unsigned<long long int>’  
 template <> struct is_unsigned<const ::boost::long_long_type> : public false_type{};  
                    ^  
/usr/include/boost/type_traits/is_unsigned.hpp:130:20: error: previous definition of ‘struct boost::is_unsigned<long long int>’  
 template <> struct is_unsigned< ::boost::long_long_type> : public false_type{};  
  
  
  
 /home/kylin/桌面/hyperscan/hyperscan/hyperscan/src/nfa/mcclellancompile.cpp line 63  
#include <boost/range/adaptor/map.hpp>

---

## Comment 1

> Username: JeffGarland  
> Created at: 2020-09-20 15:20:52 UTC  
> Url: https://github.com/boostorg/date_time/issues/173#issuecomment-695799176  

The context is better this time, and now it is clear that this is the wrong place to report this issue.  If anywhere this should be filed under boostorg/range repo, not date time. Ideally you would post a small main program on godbolt which reproduces the issue you are seeing.
