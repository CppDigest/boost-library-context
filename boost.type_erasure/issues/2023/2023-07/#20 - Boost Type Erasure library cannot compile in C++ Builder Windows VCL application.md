# #20 - Boost Type Erasure library cannot compile in C++ Builder Windows VCL application [Closed]

> Username: AndyBell1  
> Created at: 2023-07-07 12:19:22 UTC  
> Updated at: 2023-07-10 21:51:51 UTC  
> Closed at: 2023-07-10 21:51:51 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/20  

I’m using the type erasure library but have an issue (which I can workaround) when compiling in Windows 10 using Embarcadero C++ Builder 11.3.  
  
My problem is that C++ Builder brings in the Windows SDK Windows.h include file in every Windows VCL UI project it creates *before* any of my code has compiled and it’s causing conflicts in boost header member11.hpp.  
  
This code is enough to cause the error:  
  
#include <boost/type_erasure/any.hpp>  
#include <boost/type_erasure/any_cast.hpp>  
#include <boost/type_erasure/builtin.hpp>  
#include <boost/type_erasure/operators.hpp>  
#include <boost/type_erasure/member.hpp>  
#include <boost/type_erasure/free.hpp>  
#include <boost/mpl/vector.hpp>  
  
namespace mynamespace {  
BOOST_TYPE_ERASURE_MEMBER(ReadStatus)  
}  
  
The error is because Windows.h defines ‘interface’ but this code from member11.hpp also uses ‘interface’:  
  
template<class P, template<class ...> class **interface**, class Sig, class Concept, class Base, class ID>  
using choose_member_interface = typename ::boost::mpl::if_c< ::boost::is_reference<P>::value,  
    typename ::boost::mpl::if_c< ::boost::type_erasure::detail::is_non_const_ref<P>::value,  
        **interface**<Sig, Concept, Base, const ID>,  
        Base  
    >::type,  
    **interface**<Sig, Concept, Base, ID>  
>::type;  
  
and the compiler complains of conflicts.  
  
Embarcadero support have told me that I can’t prevent Windows.h being included in my project before my boost-using code is compiled and attempts I’ve made to circumvent the problem, such as adding these lines to codegear.hpp:  
  
#ifdef interface  
#undef interface  
#endif  
  
don't work because C++ Builder is still including Windows.h before my code is compiled.  
  
**To get around the problem, I changed member11.hpp in my boost installation to**  
  
template<class P, template<class ...> class **interface_**, class Sig, class Concept, class Base, class ID>  
using choose_member_interface = typename ::boost::mpl::if_c< ::boost::is_reference<P>::value,  
    typename ::boost::mpl::if_c< ::boost::type_erasure::detail::is_non_const_ref<P>::value,  
        **interface_**<Sig, Concept, Base, const ID>,  
        Base  
    >::type,  
    **interface_**<Sig, Concept, Base, ID>  
>::type;  
  
Embarcadero support said:  
“Boost brings  windows.h in because boost pulls it, *IF* the application is compiled for multi-threading then boost needs to access the OS threading functions, this is when you compile  *without* the VCL.”  
  
and I have confirmed this by creating a simple, multi-threaded Windows console application in C++ Builder…  
  
Joaquin Lopez on the boost-users mailing list asked I raise this as an issue here...  
  
Andy
