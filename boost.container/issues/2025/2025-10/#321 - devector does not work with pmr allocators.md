# #321 - devector does not work with pmr allocators [Closed]

> Username: igaztanaga  
> Created at: 2025-10-28 14:29:42 UTC  
> Updated at: 2025-10-28 14:51:11 UTC  
> Closed at: 2025-10-28 14:51:11 UTC  
> Url: https://github.com/boostorg/container/issues/321  

The following example does not compile:  
  
```  
#include <boost/container/pmr/devector.hpp>  
#include <boost/container/detail/type_traits.hpp>  
  
int main()  
{  
   using namespace boost::container;  
   using boost::container::dtl::is_same;  
  
   typedef devector<int, pmr::polymorphic_allocator<int> > intcontainer_t;  
   BOOST_CONTAINER_STATIC_ASSERT(( is_same<intcontainer_t, pmr::devector_of<int>::type >::value ));  
   #if !defined(BOOST_NO_CXX11_TEMPLATE_ALIASES)  
      BOOST_CONTAINER_STATIC_ASSERT(( is_same<intcontainer_t, pmr::devector<int> >::value ));  
   #endif  
  
   intcontainer_t cont(pmr::get_default_resource());  
   typedef typename intcontainer_t::value_type value_type;  
   cont.push_back(value_type());  
   return 0;  
}  
```  
The utilities from `boost/container/pmr/devector.hpp` are not correctly defined.
