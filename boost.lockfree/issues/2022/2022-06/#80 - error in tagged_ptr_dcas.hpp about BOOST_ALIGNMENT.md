# #80 - error in tagged_ptr_dcas.hpp about BOOST_ALIGNMENT [Open]

> Username: gstvrmrlpz  
> Created at: 2022-06-04 10:08:24 UTC  
> Updated at: 2022-07-09 21:06:03 UTC  
> Url: https://github.com/boostorg/lockfree/issues/80  

#include <boost/lockfree/detail/tagged_ptr_dcas.hpp>  
  
int main() {}  
  
/*  
[gustavo@casa bug]$ g++ stack.cc -o stack  
En el fichero incluido desde stack.cc:1:  
/usr/include/boost/lockfree/detail/tagged_ptr_dcas.hpp:30:17: error: expected unqualified-id before numeric constant  
   30 | BOOST_ALIGNMENT(2 * sizeof(void*))  
      |                 ^  
/usr/include/boost/lockfree/detail/tagged_ptr_dcas.hpp:30:17: error: expected ‘)’ before numeric constant  
   30 | BOOST_ALIGNMENT(2 * sizeof(void*))  
      |                ~^  
      |                 )  
*/
