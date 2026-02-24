# #6 - Boost.Proto 1.68.0 - display_expr undefined reference symbol [Closed]

> Username: praveen-velliengiri  
> Created at: 2018-11-01 17:47:55 UTC  
> Updated at: 2018-11-01 19:14:09 UTC  
> Closed at: 2018-11-01 19:14:09 UTC  
> Url: https://github.com/boostorg/proto/issues/6  

Hi   
I'm going some basic examples.  
```  
#include <iostream>  
  
#include <boost/proto/proto.hpp>  
  
struct variable_tag {};  
  
using namespace boost;  
  
proto::terminal<variable_tag>::type _x = {{}};  
  
int main(int argc, char const *argv[])  
{  
  
    _x %= _x + 3 * ~(_x >> 6);  
  
    proto::display_expr( _x+2 );  
}   
  
  
It compiles fine, but linker throwing undefined reference symbol.   
\/tmp/ccn7qlLW.o: In function `void boost::proto::detail::display_expr_impl::impl<boost::proto::exprns_::expr<boost::proto::tagns_::tag::plus, boost::proto::argsns_::list2<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<variable_tag>, 0l>&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<int const&>, 0l> >, 2l>, mpl_::long_<2l> >(boost::proto::exprns_::expr<boost::proto::tagns_::tag::plus, boost::proto::argsns_::list2<boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<variable_tag>, 0l>&, boost::proto::exprns_::expr<boost::proto::tagns_::tag::terminal, boost::proto::argsns_::term<int const&>, 0l> >, 2l> const&, mpl_::long_<2l>) const':  
none1.cpp:(.text._ZNK5boost5proto6detail17display_expr_impl4implINS0_7exprns_4exprINS0_6tagns_3tag4plusENS0_7argsns_5list2IRNS5_INS7_8terminalENS9_4termI12variable_tagEELl0EEENS5_ISB_NSC_IRKiEELl0EEEEELl2EEEN4mpl_5long_ILl2EEEEEvRKT_T0_[_ZNK5boost5proto6detail17display_expr_impl4implINS0_7exprns_4exprINS0_6tagns_3tag4plusENS0_7argsns_5list2IRNS5_INS7_8terminalENS9_4termI12variable_tagEELl0EEENS5_ISB_NSC_IRKiEELl0EEEEELl2EEEN4mpl_5long_ILl2EEEEEvRKT_T0_]+0xd3): undefined reference to `boost::proto::detail::display_expr_impl::display_expr_impl(boost::proto::detail::display_expr_impl const&)'  
collect2: error: ld returned 1 exit status  
  
**The display_expr_impl (display_expr_impl const &) is declared as a private member and not defined.**   
  
Is there any way to resolve this issue ?   
Thanks

---

## Comment 1

> Username: Burgos  
> Created at: 2018-11-01 18:57:10 UTC  
> Url: https://github.com/boostorg/proto/issues/6#issuecomment-435148342  

Fixed in https://github.com/boostorg/proto/pull/7

---

## Comment 2

> Username: praveen-velliengiri  
> Created at: 2018-11-01 19:14:09 UTC  
> Url: https://github.com/boostorg/proto/issues/6#issuecomment-435153517  

It's working, thanks :+1:
