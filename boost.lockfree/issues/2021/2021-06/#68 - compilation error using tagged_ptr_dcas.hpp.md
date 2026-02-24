# #68 - compilation error using tagged_ptr_dcas.hpp [Open]

> Username: gstvrmrlpz  
> Created at: 2021-06-14 06:42:17 UTC  
> Updated at: 2021-06-15 08:43:58 UTC  
> Url: https://github.com/boostorg/lockfree/issues/68  

this code...  
  
#include <boost/lockfree/detail/tagged_ptr_dcas.hpp>  
  
int main(int argc, char **argv)  
{  
    int i = argc;      
    return i;  
}  
  
generate this error...  
  
[gustavo@casa c++]$ g++ boost.cc -o boost  
En el fichero incluido desde boost.cc:1:  
/usr/include/boost/lockfree/detail/tagged_ptr_dcas.hpp:30:17: error: expected unqualified-id before numeric constant  
   30 | BOOST_ALIGNMENT(2 * sizeof(void*))  
      |                 ^  
/usr/include/boost/lockfree/detail/tagged_ptr_dcas.hpp:30:17: error: expected ‘)’ before numeric constant  
   30 | BOOST_ALIGNMENT(2 * sizeof(void*))  
      |                ~^  
      |                 )
