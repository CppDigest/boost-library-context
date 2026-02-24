# #9 Fix gcc 9 deprecated copy warnings. [Open]

> Username: Romain-Geissler-1A  
> Created at: 2019-03-22 23:18:56 UTC  
> Updated at: 2022-03-24 01:45:40 UTC  
> Url: https://github.com/boostorg/xpressive/pull/9  

Hi,  
  
When building with gcc 9, we get some of these new deprecated copy warnings. Explicitly defining the copy constructors silences them.  
  
Cheers,  
Romain

---

## Comment 1

> Username: orivej  
> Created_at: 2020-06-06 08:54:40 UTC  
> Url: https://github.com/boostorg/xpressive/pull/9#issuecomment-640015188  

It seems that `operator=();` is meant to prohibit assignment and should be deleted instead: `simple_repeat_matcher &operator =(simple_repeat_matcher const &) = delete;`. The same applies to all matchers, and probably to other declarations of `operator=();` without definition.

---

## Comment 2

> Username: Romain-Geissler-1A  
> Created_at: 2020-06-08 09:49:10 UTC  
> Url: https://github.com/boostorg/xpressive/pull/9#issuecomment-640497767  

@orivej Indeed I made this PR too quickly...  
  
I have updated it. I grepped for all "operator=" calls, and explicitly deleted it if it was a private non implemented one.

---

## Comment 3

> Username: Romain-Geissler-1A  
> Created_at: 2020-12-01 21:54:14 UTC  
> Url: https://github.com/boostorg/xpressive/pull/9#issuecomment-736844326  

Ping

---

## Comment 4

> Username: arghness  
> Created_at: 2022-03-24 01:45:40 UTC  
> Url: https://github.com/boostorg/xpressive/pull/9#issuecomment-1076984847  

I'm having what seem to be related warnings. ( Boost 1.78, gcc 11.2.1 compiling with -Wextra ). I tried applying this pull request but still got warnings with the following code:  
  
``` cpp  
#include <boost/xpressive/xpressive_static.hpp>  
  
int  
main(int /*argc*/, char * /*argv*/ [])  
{  
  return 0;  
}  
```  
  
```  
In file included from /opt/boost/include/boost/xpressive/xpressive_static.hpp:24,  
                 from test.cpp:1:  
/opt/boost/include/boost/xpressive/regex_primitives.hpp: In static member function 'static boost::xpressive::detail::basic_mark_tag boost::xpressive::mark_tag::make_tag(int)':  
/opt/boost/include/boost/xpressive/regex_primitives.hpp:548:16: warning: implicitly-declared 'constexpr boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::xpressive::detail::mark_placeholder>, 0>::expr(const boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::xpressive::detail::mark_placeholder>, 0>&)' is deprecated [-Wdeprecated-copy]  
  548 |         return mark;  
      |                ^~~~  
In file included from /opt/boost/include/boost/proto/detail/expr.hpp:4,  
                 from /opt/boost/include/boost/proto/expr.hpp:145,  
                 from /opt/boost/include/boost/proto/core.hpp:17,  
                 from /opt/boost/include/boost/xpressive/regex_primitives.hpp:28,  
                 from /opt/boost/include/boost/xpressive/xpressive_static.hpp:24,  
                 from test.cpp:1:  
/opt/boost/include/boost/proto/detail/preprocessed/expr_variadic.hpp:96:9: note: because 'boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::xpressive::detail::mark_placeholder>, 0>' has user-provided 'const boost::proto::exprns_::expr<boost::proto::tagns_::tag::assign, boost::proto::argsns_::list2<boost::proto::exprns_::expr<Tag, boost::proto::argsns_::term<Arg>, 0>&, const boost::proto::exprns_::expr<Tag, boost::proto::argsns_::term<Arg>, 0>&>, 2> boost::proto::exprns_::expr<Tag, boost::proto::argsns_::term<Arg>, 0>::operator=(const boost::proto::exprns_::expr<Tag, boost::proto::argsns_::term<Arg>, 0>&) [with Tag = boost::proto::tagns_::tag::terminal; Arg0 = boost::xpressive::detail::mark_placeholder]'  
   96 |         operator =(expr const &a)  
      |         ^~~~~~~~  
...  
...  
...  
```  
  
The proto expr::operator=() is actually defined and required. I tried adding a copy constructor, as a test, but that led to another issue where the class is being list initialized and so that wasn't possible with a copy constructor.  
  
I think the only workaround is to disable that warning for now.

---
