# #302 - Double-move problems with -fno-exceptions on Apple Clang [Closed]

> Username: ldionne  
> Created at: 2016-10-08 20:03:32 UTC  
> Updated at: 2016-10-09 20:09:45 UTC  
> Closed at: 2016-10-09 20:09:45 UTC  
> Url: https://github.com/boostorg/hana/issues/302  

Building some tests with Apple Clang with `-fno-exceptions` triggers double-moves. This was not caught because we only build with `-fno-exceptions` on Linux with vanilla Clang.  
  
``` c++  
clang++ -std=c++14 -I test/_include -I include -fno-exceptions -xc++ - <<EOF  
#include <boost/hana/bool.hpp>  
#include <boost/hana/equal.hpp>  
#include <boost/hana/functional/always.hpp>  
#include <boost/hana/tuple.hpp>  
  
#include <laws/monad_plus.hpp>  
#include <support/seq.hpp>  
namespace hana = boost::hana;  
using hana::test::ct_eq;  
using hana::test::ct_ord;  
  
  
int main() {  
    auto eqs = hana::make_tuple(  
          ::seq()  
        , ::seq(ct_eq<0>{})  
        , ::seq(ct_eq<0>{}, ct_eq<1>{})  
        , ::seq(ct_eq<0>{}, ct_eq<1>{}, ct_eq<2>{})  
        , ::seq(ct_eq<0>{}, ct_eq<1>{}, ct_eq<2>{}, ct_eq<3>{})  
    );  
  
    auto eq_keys = hana::make_tuple(ct_eq<0>{}, ct_eq<3>{}, ct_eq<10>{});  
  
    auto predicates = hana::make_tuple(  
        hana::equal.to(ct_eq<0>{}), hana::equal.to(ct_eq<3>{}), hana::equal.to(ct_eq<10>{}),  
        hana::always(hana::true_c), hana::always(hana::false_c)  
    );  
  
    hana::test::TestMonadPlus<::Seq>{eqs, predicates, eq_keys};  
}  
EOF  
```
