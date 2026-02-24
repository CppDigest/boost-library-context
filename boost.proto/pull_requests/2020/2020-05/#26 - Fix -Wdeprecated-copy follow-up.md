# #26 Fix -Wdeprecated-copy follow-up [Merged]

> Username: Kojoley  
> Created at: 2020-05-03 23:54:33 UTC  
> Updated at: 2020-05-29 17:45:21 UTC  
> Merged at: 2020-05-29 17:43:49 UTC  
> Closed at: 2020-05-29 17:43:49 UTC  
> Url: https://github.com/boostorg/proto/pull/26  

This one did not show up in Proto tests, but in Spirit.

---

## Comment 1

> Username: ericniebler  
> Created_at: 2020-05-04 18:52:14 UTC  
> Url: https://github.com/boostorg/proto/pull/26#issuecomment-623641278  

Can you give me a little more context about what motivates this change?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2020-05-04 19:06:58 UTC  
> Url: https://github.com/boostorg/proto/pull/26#issuecomment-623648477  

https://wandbox.org/permlink/6HE9kgFvucFCEjEG  
```  
boost/proto/extends.hpp:544:13: warning: definition of implicit copy assignment operator for 'extends<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<boost::spirit::qi::reference<const boost::spirit::repository::qi::subrule<0, boost::spirit::unused_type, boost::spirit::unused_type>>>, 0>, boost::spirit::repository::qi::subrule<0, boost::spirit::unused_type, boost::spirit::unused_type>, boost::proto::domainns_::default_domain, 0>' is deprecated because it has a user-declared copy constructor [-Wdeprecated-copy]  
            extends(extends const &that)  
            ^  
```

---

## Comment 3

> Username: Kojoley  
> Created_at: 2020-05-04 19:32:00 UTC  
> Url: https://github.com/boostorg/proto/pull/26#issuecomment-623660556  

Based on `proto/test/mpl.cpp` repro:  
```cpp  
#include <boost/proto/proto.hpp>  
  
namespace proto = boost::proto;  
  
template<class E>  
struct my_expr  
  : proto::extends<E, my_expr<E> >  
{  
    my_expr(E const &e = E())  
      : proto::extends<E, my_expr<E>>(e)  
    {}  
};  
  
int main()  
{  
    my_expr<proto::terminal<int>::type> i;  
    i = i;  
}  
```  
https://wandbox.org/permlink/NgDtn7Shq1H0SFuz

---

## Comment 4

> Username: Kojoley  
> Created_at: 2020-05-29 13:17:27 UTC  
> Url: https://github.com/boostorg/proto/pull/26#issuecomment-635967149  

Ping

---
