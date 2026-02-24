# #62 - lambda_tests3a fails on VS2015/2017 [Closed]

> Username: Kojoley  
> Created at: 2018-01-03 13:59:25 UTC  
> Updated at: 2018-02-06 18:11:25 UTC  
> Closed at: 2018-02-01 01:00:00 UTC  
> Url: https://github.com/boostorg/phoenix/issues/62  

http://www.boost.org/development/tests/develop/developer/output/teeks99-09-p-win2012R2-64on64-boost-bin-v2-libs-phoenix-test-lambda_tests3a-test-msvc-14-1-dbg-adrs-mdl-64-thrdp-wn32-thrd-mlt.html  
http://www.boost.org/development/tests/develop/developer/output/teeks99-09-f-win2012R2-64on64-boost-bin-v2-libs-phoenix-test-lambda_tests3a-test-msvc-14-0-dbg-adrs-mdl-64-thrdp-wn32-thrd-mlt.html  
  
This does not fix anything:  
```cpp  
# define BOOST_NO_CXX11_VARIADIC_TEMPLATES  
# define BOOST_NO_CXX11_RVALUE_REFERENCES  
# define BOOST_PHOENIX_NO_VARIADIC_ACTOR  
# define BOOST_PHOENIX_NO_VARIADIC_CALL  
# define BOOST_PHOENIX_NO_VARIADIC_FUNCTION_EQUAL  
# define BOOST_PHOENIX_NO_VARIADIC_FUNCTION_EVAL  
# define BOOST_PHOENIX_NO_VARIADIC_PHX2_RESULT  
# define BOOST_PHOENIX_NO_VARIADIC_EXPRESSION  
# define BOOST_PHOENIX_NO_VARIADIC_BIND  
```  
  
It look to me like a proto bug.

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-01-03 21:54:00 UTC  
> Url: https://github.com/boostorg/phoenix/issues/62#issuecomment-355138260  

Well, my knowledge of proto is nearly zero and I do not know is `phoenix_generator` definition valid, but replacing it with `struct phoenix_generator : proto::pod_generator<actor>` fixes the `lambda_tests3a` test.  
After discovering this I tried defining `result` in `phoenix_generator` and it worked.  
```cpp  
        template<typename Sig>  
        struct result;  
  
        template<typename This, typename Expr>  
        struct result<This(Expr)>  
        {  
            typedef actor<typename proto::detail::uncvref<Expr>::type> type;  
        };  
```  
  
Hope it helps.

---

## Comment 2

> Username: Flast  
> Created at: 2018-01-04 00:41:47 UTC  
> Url: https://github.com/boostorg/phoenix/issues/62#issuecomment-355168571  

blocks #60

---

## Comment 3

> Username: Flast  
> Created at: 2018-01-04 13:33:28 UTC  
> Url: https://github.com/boostorg/phoenix/issues/62#issuecomment-355283000  

note that #58 and #61 are independent of this

---

## Comment 4

> Username: Flast  
> Created at: 2018-01-06 07:29:24 UTC  
> Url: https://github.com/boostorg/phoenix/issues/62#issuecomment-355729723  

I tried this code and fail with error message `base class undefined` (undefined?! wtf).  
  
```cpp  
#include <boost/phoenix.hpp>  
using namespace boost;  
using namespace boost::proto;  
#include <iostream>  
#include <boost/core/demangle.hpp>  
#include <typeinfo>  
  
int main()  
{  
    std::cout << boost::core::demangle(  
    typeid(  
      phoenix::phoenix_generator::impl<  
        exprns_::basic_expr<  
            tagns_::tag::assign,  
            argsns_::list2<exprns_::basic_expr<phoenix::tag::lambda,argsns_::list4<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<const phoenix::actor<exprns_::basic_expr<phoenix::tag::let_,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<phoenix::actor<exprns_::basic_expr<phoenix::tag::lambda_actor,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,3>>>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<phoenix::detail::local<phoenix::local_names::_a_key>>>,0>,phoenix::actor<exprns_::basic_expr<phoenix::detail::tag::function_eval,argsns_::list1<phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::local<phoenix::local_names::_a_key>>,0>>>,1>>>,3>> *>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,4>,phoenix::actor<exprns_::basic_expr<phoenix::tag::lambda,argsns_::list4<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<const phoenix::actor<exprns_::basic_expr<phoenix::tag::let_,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<phoenix::actor<exprns_::basic_expr<phoenix::tag::lambda_actor,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,3>>>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<phoenix::detail::local<phoenix::local_names::_a_key>>>,0>,phoenix::actor<exprns_::basic_expr<phoenix::detail::tag::function_eval,argsns_::list1<phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::local<phoenix::local_names::_a_key>>,0>>>,1>>>,3>> *>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,4>>>,  
2>, empty_state, empty_env  
      >::result_type  
      ).name()) << std::endl;  
}  
```  
  
However, expanding `impl<Expr, empty_state, empty_env>` to `case_<Tag>::impl<Expr, empty_state, empty_env>` works...  
  
```cpp  
#include <boost/phoenix.hpp>  
using namespace boost;  
using namespace boost::proto;  
#include <iostream>  
#include <boost/core/demangle.hpp>  
#include <typeinfo>  
  
int main()  
{  
    std::cout << boost::core::demangle(  
    typeid(  
      phoenix::phoenix_generator::case_<tagns_::tag::assign>::impl<  
        exprns_::basic_expr<  
            tagns_::tag::assign,  
            argsns_::list2<exprns_::basic_expr<phoenix::tag::lambda,argsns_::list4<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<const phoenix::actor<exprns_::basic_expr<phoenix::tag::let_,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<phoenix::actor<exprns_::basic_expr<phoenix::tag::lambda_actor,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,3>>>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<phoenix::detail::local<phoenix::local_names::_a_key>>>,0>,phoenix::actor<exprns_::basic_expr<phoenix::detail::tag::function_eval,argsns_::list1<phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::local<phoenix::local_names::_a_key>>,0>>>,1>>>,3>> *>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,4>,phoenix::actor<exprns_::basic_expr<phoenix::tag::lambda,argsns_::list4<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<const phoenix::actor<exprns_::basic_expr<phoenix::tag::let_,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<phoenix::actor<exprns_::basic_expr<phoenix::tag::lambda_actor,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,3>>>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<phoenix::detail::local<phoenix::local_names::_a_key>>>,0>,phoenix::actor<exprns_::basic_expr<phoenix::detail::tag::function_eval,argsns_::list1<phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::local<phoenix::local_names::_a_key>>,0>>>,1>>>,3>> *>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,4>>>,  
2>, empty_state, empty_env  
      >::result_type  
      ).name()) << std::endl;  
}  
```  
  
confirmed with msvc 12.0/14.0/14.1, note 12.0 works as expected both of above cases.

---

## Comment 5

> Username: fletchjp  
> Created at: 2018-01-06 21:12:06 UTC  
> Url: https://github.com/boostorg/phoenix/issues/62#issuecomment-355777263  

I can offer some thoughts on this.  About three years ago I was working on these problems, and attempting to understand the difference between the let and lambda within Phoenix.  
  
I cut the lambda_tests up into smaller and smaller chunks to isolate the problems.  I came to the conclusion that some of the problems occur when the compiler optimizes.  I think that somewhere along the line a temporary gets optimised away which is still needed.  
  
In lambda_tests3a there is this test:  
  
            int x = (let(_a = lambda[val(1)])[bind(_a)])();  
            BOOST_TEST(x == 1);  
  
This works for me with clang 3.5 but fails with clang 3.5 -O2.  
  
This alternative test works with clang 3.5 and -O2.  
  
            int x = (let(_a = _1)[bind(_a)])(lambda[val(1)]());  
            BOOST_TEST(x == 1);  
  
  
The version of lambda_tests3a on develop has this code for which I take responsibility.  It is my attempt to detect compiler optimisation and switch the test.  That may not work for all compilers.  
  
Around that time I gave up trying to figure out what was wrong and you may want to take out my fudge.  
  
#if defined(__OPTIMIZE__) && __OPTIMIZE__  
int x = (let(_a = _1)[bind(_a)])(lambda[val(1)]());  
#else  
int x = (let(_a = lambda[val(1)])[bind(_a)])();  
#endif  
  
________________________________  
From: Kohei Takahashi [notifications@github.com]  
Sent: 06 January 2018 07:29  
To: boostorg/phoenix  
Cc: Subscribed  
Subject: Re: [boostorg/phoenix] lambda_tests3a fails on VS2015/2017 (#62)  
  
  
I tried this code and fail with error message base class undefined (undefined?! wtf).  
  
#include <boost/phoenix.hpp>  
using namespace boost;  
using namespace boost::proto;  
#include <iostream>  
#include <boost/core/demangle.hpp>  
#include <typeinfo>  
  
int main()  
{  
    std::cout << boost::core::demangle(  
    typeid(  
      phoenix::phoenix_generator::impl<  
        exprns_::basic_expr<  
            tagns_::tag::assign,  
            argsns_::list2<exprns_::basic_expr<phoenix::tag::lambda,argsns_::list4<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<const phoenix::actor<exprns_::basic_expr<phoenix::tag::let_,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<phoenix::actor<exprns_::basic_expr<phoenix::tag::lambda_actor,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,3>>>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<phoenix::detail::local<phoenix::local_names::_a_key>>>,0>,phoenix::actor<exprns_::basic_expr<phoenix::detail::tag::function_eval,argsns_::list1<phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::local<phoenix::local_names::_a_key>>,0>>>,1>>>,3>> *>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,4>,phoenix::actor<exprns_::basic_expr<phoenix::tag::lambda,argsns_::list4<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<const phoenix::actor<exprns_::basic_expr<phoenix::tag::let_,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<phoenix::actor<exprns_::basic_expr<phoenix::tag::lambda_actor,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,3>>>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<phoenix::detail::local<phoenix::local_names::_a_key>>>,0>,phoenix::actor<exprns_::basic_expr<phoenix::detail::tag::function_eval,argsns_::list1<phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::local<phoenix::local_names::_a_key>>,0>>>,1>>>,3>> *>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,4>>>,  
2>, empty_state, empty_env  
      >::result_type  
      ).name()) << std::endl;  
}  
  
However, expanding impl<Expr, empty_state, empty_env> to case_<Tag>::impl<Expr, empty_state, empty_env> works...  
  
#include <boost/phoenix.hpp>  
using namespace boost;  
using namespace boost::proto;  
#include <iostream>  
#include <boost/core/demangle.hpp>  
#include <typeinfo>  
  
int main()  
{  
    std::cout << boost::core::demangle(  
    typeid(  
      phoenix::phoenix_generator::case_<tagns_::tag::assign>::impl<  
        exprns_::basic_expr<  
            tagns_::tag::assign,  
            argsns_::list2<exprns_::basic_expr<phoenix::tag::lambda,argsns_::list4<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<const phoenix::actor<exprns_::basic_expr<phoenix::tag::let_,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<phoenix::actor<exprns_::basic_expr<phoenix::tag::lambda_actor,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,3>>>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<phoenix::detail::local<phoenix::local_names::_a_key>>>,0>,phoenix::actor<exprns_::basic_expr<phoenix::detail::tag::function_eval,argsns_::list1<phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::local<phoenix::local_names::_a_key>>,0>>>,1>>>,3>> *>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,4>,phoenix::actor<exprns_::basic_expr<phoenix::tag::lambda,argsns_::list4<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<const phoenix::actor<exprns_::basic_expr<phoenix::tag::let_,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<phoenix::actor<exprns_::basic_expr<phoenix::tag::lambda_actor,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,3>>>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<phoenix::detail::local<phoenix::local_names::_a_key>>>,0>,phoenix::actor<exprns_::basic_expr<phoenix::detail::tag::function_eval,argsns_::list1<phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::local<phoenix::local_names::_a_key>>,0>>>,1>>>,3>> *>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,4>>>,  
2>, empty_state, empty_env  
      >::result_type  
      ).name()) << std::endl;  
}  
  
confirmed with msvc 12.0/14.0/14.1, note 12.0 works as expected both of above cases.  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub<https://emea01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fboostorg%2Fphoenix%2Fissues%2F62%23issuecomment-355729723&data=02%7C01%7Cj.p.fletcher%40aston.ac.uk%7C3b9cadd584574f4f098708d554d7383b%7Ca085950c4c2544d5945ab852fa44a221%7C0%7C0%7C636508205692028063&sdata=7cr5pedjiw2xwBosmeuvjs4GR2ej1xLzN07P2oVOsIY%3D&reserved=0>, or mute the thread<https://emea01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fnotifications%2Funsubscribe-auth%2FAGG23yl63_8r6TMiqsgcBKNLHlU8WdaJks5tHyDVgaJpZM4RRwyd&data=02%7C01%7Cj.p.fletcher%40aston.ac.uk%7C3b9cadd584574f4f098708d554d7383b%7Ca085950c4c2544d5945ab852fa44a221%7C0%7C0%7C636508205692028063&sdata=pvWM%2BHj1%2BibfXqd6i%2BU7P%2BK6DpxqwWdS3lhWItd5naw%3D&reserved=0>.

---

## Comment 6

> Username: fletchjp  
> Created at: 2018-01-06 21:27:55 UTC  
> Url: https://github.com/boostorg/phoenix/issues/62#issuecomment-355778749  

Also note that lamba_test3b runs the modified test and is not failing.  
  
John  
  
________________________________  
From: Fletcher, John P  
Sent: 06 January 2018 21:11  
To: boostorg/phoenix; boostorg/phoenix  
Cc: Subscribed  
Subject: RE: [boostorg/phoenix] lambda_tests3a fails on VS2015/2017 (#62)  
  
I can offer some thoughts on this.  About three years ago I was working on these problems, and attempting to understand the difference between the let and lambda within Phoenix.  
  
I cut the lambda_tests up into smaller and smaller chunks to isolate the problems.  I came to the conclusion that some of the problems occur when the compiler optimizes.  I think that somewhere along the line a temporary gets optimised away which is still needed.  
  
In lambda_tests3a there is this test:  
  
            int x = (let(_a = lambda[val(1)])[bind(_a)])();  
            BOOST_TEST(x == 1);  
  
This works for me with clang 3.5 but fails with clang 3.5 -O2.  
  
This alternative test works with clang 3.5 and -O2.  
  
            int x = (let(_a = _1)[bind(_a)])(lambda[val(1)]());  
            BOOST_TEST(x == 1);  
  
  
The version of lambda_tests3a on develop has this code for which I take responsibility.  It is my attempt to detect compiler optimisation and switch the test.  That may not work for all compilers.  
  
Around that time I gave up trying to figure out what was wrong and you may want to take out my fudge.  
  
#if defined(__OPTIMIZE__) && __OPTIMIZE__  
int x = (let(_a = _1)[bind(_a)])(lambda[val(1)]());  
#else  
int x = (let(_a = lambda[val(1)])[bind(_a)])();  
#endif  
  
________________________________  
From: Kohei Takahashi [notifications@github.com]  
Sent: 06 January 2018 07:29  
To: boostorg/phoenix  
Cc: Subscribed  
Subject: Re: [boostorg/phoenix] lambda_tests3a fails on VS2015/2017 (#62)  
  
  
I tried this code and fail with error message base class undefined (undefined?! wtf).  
  
#include <boost/phoenix.hpp>  
using namespace boost;  
using namespace boost::proto;  
#include <iostream>  
#include <boost/core/demangle.hpp>  
#include <typeinfo>  
  
int main()  
{  
    std::cout << boost::core::demangle(  
    typeid(  
      phoenix::phoenix_generator::impl<  
        exprns_::basic_expr<  
            tagns_::tag::assign,  
            argsns_::list2<exprns_::basic_expr<phoenix::tag::lambda,argsns_::list4<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<const phoenix::actor<exprns_::basic_expr<phoenix::tag::let_,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<phoenix::actor<exprns_::basic_expr<phoenix::tag::lambda_actor,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,3>>>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<phoenix::detail::local<phoenix::local_names::_a_key>>>,0>,phoenix::actor<exprns_::basic_expr<phoenix::detail::tag::function_eval,argsns_::list1<phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::local<phoenix::local_names::_a_key>>,0>>>,1>>>,3>> *>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,4>,phoenix::actor<exprns_::basic_expr<phoenix::tag::lambda,argsns_::list4<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<const phoenix::actor<exprns_::basic_expr<phoenix::tag::let_,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<phoenix::actor<exprns_::basic_expr<phoenix::tag::lambda_actor,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,3>>>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<phoenix::detail::local<phoenix::local_names::_a_key>>>,0>,phoenix::actor<exprns_::basic_expr<phoenix::detail::tag::function_eval,argsns_::list1<phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::local<phoenix::local_names::_a_key>>,0>>>,1>>>,3>> *>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,4>>>,  
2>, empty_state, empty_env  
      >::result_type  
      ).name()) << std::endl;  
}  
  
However, expanding impl<Expr, empty_state, empty_env> to case_<Tag>::impl<Expr, empty_state, empty_env> works...  
  
#include <boost/phoenix.hpp>  
using namespace boost;  
using namespace boost::proto;  
#include <iostream>  
#include <boost/core/demangle.hpp>  
#include <typeinfo>  
  
int main()  
{  
    std::cout << boost::core::demangle(  
    typeid(  
      phoenix::phoenix_generator::case_<tagns_::tag::assign>::impl<  
        exprns_::basic_expr<  
            tagns_::tag::assign,  
            argsns_::list2<exprns_::basic_expr<phoenix::tag::lambda,argsns_::list4<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<const phoenix::actor<exprns_::basic_expr<phoenix::tag::let_,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<phoenix::actor<exprns_::basic_expr<phoenix::tag::lambda_actor,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,3>>>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<phoenix::detail::local<phoenix::local_names::_a_key>>>,0>,phoenix::actor<exprns_::basic_expr<phoenix::detail::tag::function_eval,argsns_::list1<phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::local<phoenix::local_names::_a_key>>,0>>>,1>>>,3>> *>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,4>,phoenix::actor<exprns_::basic_expr<phoenix::tag::lambda,argsns_::list4<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<const phoenix::actor<exprns_::basic_expr<phoenix::tag::let_,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector1<phoenix::actor<exprns_::basic_expr<phoenix::tag::lambda_actor,argsns_::list3<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,3>>>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<phoenix::detail::local<phoenix::local_names::_a_key>>>,0>,phoenix::actor<exprns_::basic_expr<phoenix::detail::tag::function_eval,argsns_::list1<phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::local<phoenix::local_names::_a_key>>,0>>>,1>>>,3>> *>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::vector0<void>>,0>,exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<phoenix::detail::map_local_index_to_tuple<>>,0>,phoenix::actor<exprns_::basic_expr<tagns_::tag::terminal,argsns_::term<int>,0>>>,4>>>,  
2>, empty_state, empty_env  
      >::result_type  
      ).name()) << std::endl;  
}  
  
confirmed with msvc 12.0/14.0/14.1, note 12.0 works as expected both of above cases.  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub<https://emea01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fboostorg%2Fphoenix%2Fissues%2F62%23issuecomment-355729723&data=02%7C01%7Cj.p.fletcher%40aston.ac.uk%7C3b9cadd584574f4f098708d554d7383b%7Ca085950c4c2544d5945ab852fa44a221%7C0%7C0%7C636508205692028063&sdata=7cr5pedjiw2xwBosmeuvjs4GR2ej1xLzN07P2oVOsIY%3D&reserved=0>, or mute the thread<https://emea01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fnotifications%2Funsubscribe-auth%2FAGG23yl63_8r6TMiqsgcBKNLHlU8WdaJks5tHyDVgaJpZM4RRwyd&data=02%7C01%7Cj.p.fletcher%40aston.ac.uk%7C3b9cadd584574f4f098708d554d7383b%7Ca085950c4c2544d5945ab852fa44a221%7C0%7C0%7C636508205692028063&sdata=pvWM%2BHj1%2BibfXqd6i%2BU7P%2BK6DpxqwWdS3lhWItd5naw%3D&reserved=0>.

---

## Comment 7

> Username: Kojoley  
> Created at: 2018-01-06 21:38:45 UTC  
> Url: https://github.com/boostorg/phoenix/issues/62#issuecomment-355779727  

The current problem with `lambda_tests3a` is in type deduction. It is the first time I hear that type deduction is affected by compiler options. I will understand if some bug in compiler spoils the things, like problems with a correct SFINAE support, but optimizer have nothing to do with the type system.  
  
> I think that somewhere along the line a temporary gets optimised away which is still needed.  
  
From my recent experience on Spirit this type of bug can be easily caught by using ASAN (`-fsanitize-address-use-after-scope`).

---

## Comment 8

> Username: fletchjp  
> Created at: 2018-01-06 21:49:06 UTC  
> Url: https://github.com/boostorg/phoenix/issues/62#issuecomment-355780525  

lambda_tests3a has compiler dependent code based on __OPTIMIZE__ (see previous message).  
  
It is possible that the MSVC 14 compiler is taking a different branch through the code (e.g. not defining __OPTIMIZE) , and so compiling something different.  I don't have access to that compiler so I am not able to check this.  
  
I suspect that there is a bug somewhere in phoenix let and or lambda, as yet not detected.  I am not currently working on this, just offering evidence that could help.  
  
John  
  
________________________________  
From: Nikita Kniazev [notifications@github.com]  
Sent: 06 January 2018 21:38  
To: boostorg/phoenix  
Cc: Fletcher, John P; Comment  
Subject: Re: [boostorg/phoenix] lambda_tests3a fails on VS2015/2017 (#62)  
  
  
The current problem with lambda_tests3a is in type deduction. It is the first time I hear that type deduction is affected by compiler options. I will understand if some bug in compiler spoils the things, like problems with a correct SFINAE support, but optimizer have nothing to do with the type system.  
  
I think that somewhere along the line a temporary gets optimised away which is still needed.  
  
From my recent experience on Spirit this type of bug can be easily caught by using ASAN (-fsanitize-address-use-after-scope).  
  
—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub<https://emea01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fboostorg%2Fphoenix%2Fissues%2F62%23issuecomment-355779727&data=02%7C01%7CJ.P.Fletcher%40aston.ac.uk%7C9cca53fe3fef48dba63608d5554dde26%7Ca085950c4c2544d5945ab852fa44a221%7C0%7C0%7C636508715286480710&sdata=wpXnUSwQsMIUKUQoG4CUHkvPc0KY4EztUpPdwRWsqHk%3D&reserved=0>, or mute the thread<https://emea01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fnotifications%2Funsubscribe-auth%2FAGG231gt66Yh4U5JW7JpFVnD0CMB0FzQks5tH-flgaJpZM4RRwyd&data=02%7C01%7CJ.P.Fletcher%40aston.ac.uk%7C9cca53fe3fef48dba63608d5554dde26%7Ca085950c4c2544d5945ab852fa44a221%7C0%7C0%7C636508715286480710&sdata=zoQ%2FELvCfOQ4gxbTS9%2FwhzW3nDs7g0VwHeznCh%2FXS3k%3D&reserved=0>.

---

## Comment 9

> Username: fletchjp  
> Created at: 2018-01-08 12:18:36 UTC  
> Url: https://github.com/boostorg/phoenix/issues/62#issuecomment-355952004  

It may help to compare with more_let_tests which runs with all compilers.  It looks as though what I was doing at that time was to reduce the number of failures.  I replaced _a with _a+0 to force it to do a calculation which changes the behaviour when optimized.  
  
Best wishes  
  
John  
  
________________________________  
From: Fletcher, John P  
Sent: 06 January 2018 21:49  
To: boostorg/phoenix; boostorg/phoenix  
Cc: Comment  
Subject: RE: [boostorg/phoenix] lambda_tests3a fails on VS2015/2017 (#62)  
  
lambda_tests3a has compiler dependent code based on __OPTIMIZE__ (see previous message).  
  
It is possible that the MSVC 14 compiler is taking a different branch through the code (e.g. not defining __OPTIMIZE) , and so compiling something different.  I don't have access to that compiler so I am not able to check this.  
  
I suspect that there is a bug somewhere in phoenix let and or lambda, as yet not detected.  I am not currently working on this, just offering evidence that could help.  
  
John  
  
________________________________  
From: Nikita Kniazev [notifications@github.com]  
Sent: 06 January 2018 21:38  
To: boostorg/phoenix  
Cc: Fletcher, John P; Comment  
Subject: Re: [boostorg/phoenix] lambda_tests3a fails on VS2015/2017 (#62)  
  
  
The current problem with lambda_tests3a is in type deduction. It is the first time I hear that type deduction is affected by compiler options. I will understand if some bug in compiler spoils the things, like problems with a correct SFINAE support, but optimizer have nothing to do with the type system.  
  
I think that somewhere along the line a temporary gets optimised away which is still needed.  
  
From my recent experience on Spirit this type of bug can be easily caught by using ASAN (-fsanitize-address-use-after-scope).  
  
—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub<https://emea01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fboostorg%2Fphoenix%2Fissues%2F62%23issuecomment-355779727&data=02%7C01%7CJ.P.Fletcher%40aston.ac.uk%7C9cca53fe3fef48dba63608d5554dde26%7Ca085950c4c2544d5945ab852fa44a221%7C0%7C0%7C636508715286480710&sdata=wpXnUSwQsMIUKUQoG4CUHkvPc0KY4EztUpPdwRWsqHk%3D&reserved=0>, or mute the thread<https://emea01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fnotifications%2Funsubscribe-auth%2FAGG231gt66Yh4U5JW7JpFVnD0CMB0FzQks5tH-flgaJpZM4RRwyd&data=02%7C01%7CJ.P.Fletcher%40aston.ac.uk%7C9cca53fe3fef48dba63608d5554dde26%7Ca085950c4c2544d5945ab852fa44a221%7C0%7C0%7C636508715286480710&sdata=zoQ%2FELvCfOQ4gxbTS9%2FwhzW3nDs7g0VwHeznCh%2FXS3k%3D&reserved=0>.

---

## Comment 10

> Username: Flast  
> Created at: 2018-01-09 13:10:34 UTC  
> Url: https://github.com/boostorg/phoenix/issues/62#issuecomment-356279563  

Anyway, switching with `__OPTIMIZE__` in test code is not good solution, test should use same code independent of compiler configuration (temporary disabling with FIXME comment is acceptable).  
  
I'll do that: disable the test with msvc 14.x in appvayor.

---

## Comment 11

> Username: fletchjp  
> Created at: 2018-01-09 14:25:26 UTC  
> Url: https://github.com/boostorg/phoenix/issues/62#issuecomment-356298073  

I agree and would not do that now.  
  
John  
  
________________________________  
From: Kohei Takahashi [notifications@github.com]  
Sent: 09 January 2018 13:10  
To: boostorg/phoenix  
Cc: Fletcher, John P; Comment  
Subject: Re: [boostorg/phoenix] lambda_tests3a fails on VS2015/2017 (#62)  
  
  
Anyway, switching with __OPTIMIZE__ in test code is not good solution, test should use same code independent of compiler configuration (temporary disabling with FIXME comment is acceptable).  
  
I'll do that: disable the test with msvc 14.x in appvayor.  
  
—  
You are receiving this because you commented.  
Reply to this email directly, view it on GitHub<https://emea01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fboostorg%2Fphoenix%2Fissues%2F62%23issuecomment-356279563&data=02%7C01%7Cj.p.fletcher%40aston.ac.uk%7Ca59fdde03e094f3daf5708d557628250%7Ca085950c4c2544d5945ab852fa44a221%7C0%7C0%7C636511002953300128&sdata=eeOARAKZ8F8VNlVwMVwsuzCb27twUkT2dxmZb9uD3Rc%3D&reserved=0>, or mute the thread<https://emea01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fnotifications%2Funsubscribe-auth%2FAGG23_rTKu_AQLO9KxWGSESaFmm6iaw-ks5tI2VLgaJpZM4RRwyd&data=02%7C01%7Cj.p.fletcher%40aston.ac.uk%7Ca59fdde03e094f3daf5708d557628250%7Ca085950c4c2544d5945ab852fa44a221%7C0%7C0%7C636511002953300128&sdata=MQ3L0p8Sll%2Ba2I5pPQ7FwtZa8C%2FvZ22546XZM%2FDwnTg%3D&reserved=0>.

---

## Comment 12

> Username: joemmett  
> Created at: 2018-01-10 23:38:35 UTC  
> Url: https://github.com/boostorg/phoenix/issues/62#issuecomment-356774664  

Hi - I'm a dev on the Microsoft compiler and we ran into this issue in our internal testing as well. I haven't completely analyzed this yet but it looks like this is a compiler bug, likely not a bug in Phoenix. The reduced repro showing pass/fail depending on whether the base class 'case_' appears in the name or not is helpful, but even with that small(er) sample the repro is quite complex and I'm not terribly familiar with Phoenix. I'll try to post an update as I make progress, but if there are any simplifications that could be made to the extremely long basic_expr<> specialization while still reproing the undefined class error that would be very helpful.

---

## Comment 13

> Username: Kojoley  
> Created at: 2018-01-25 17:29:12 UTC  
> Url: https://github.com/boostorg/phoenix/issues/62#issuecomment-360539264  

> if there are any simplifications that could be made to the extremely long basic_expr<> specialization while still reproing the undefined class error that would be very helpful.  
  
```cpp  
    typedef boost::proto::basic_expr<  
        boost::proto::tag::terminal  
      , boost::proto::term<int>  
      , 0  
    > somexpr;  
  
#if 1  
    boost::phoenix::phoenix_generator::impl<  
#else  
    boost::phoenix::phoenix_generator::case_<boost::proto::tagns_::tag::assign>::impl<  
#endif  
        boost::proto::basic_expr<  
            boost::proto::tag::assign  
          , boost::proto::list2<somexpr, boost::phoenix::actor<somexpr> >  
          , 2  
        >  
      , boost::proto::empty_state  
      , boost::proto::empty_env  
    >  
```  
  
P.S. I have previously posted and deleted a comment with a malfunction test case, this one should be good and the error message is pretty similar to original one.

---

## Comment 14

> Username: Kojoley  
> Created at: 2018-01-26 15:49:07 UTC  
> Url: https://github.com/boostorg/phoenix/issues/62#issuecomment-360821539  

This is actual thing that does not work.  
```cpp  
    typedef boost::proto::basic_expr<  
        boost::proto::tag::terminal  
      , boost::proto::term<int>  
      , 0  
    > somexpr;  
  
    boost::proto::detail::make_expr_<  
        boost::proto::tag::assign  
      , boost::phoenix::phoenix_domain  
      , somexpr  
      , boost::phoenix::actor<somexpr>  
    >  
```  
  
The template expansion goes into a recursion on newest VS and fine in older versions (I have failed to debug any further). But, it looks like I have found some bug in Phoenix, or at least a patch fixes the particular problem.
