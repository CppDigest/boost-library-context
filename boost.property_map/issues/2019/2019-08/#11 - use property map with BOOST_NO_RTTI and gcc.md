# #11 - use property map with BOOST_NO_RTTI and gcc [Closed]

> Username: mfurmann  
> Created at: 2019-08-16 10:27:03 UTC  
> Updated at: 2022-04-23 22:48:04 UTC  
> Closed at: 2022-04-23 22:48:04 UTC  
> Url: https://github.com/boostorg/property_map/issues/11  

dynamic_property_map.hpp fails to compile with BOOST_NO_RTTI defined using clang or gcc.  
  
Example program:  
`  
#define BOOST_NO_RTTI  
#include <boost/property_map/dynamic_property_map.hpp>  
  
int main()  
{  
}  
`  
Error message with gcc 10:  
boost/property_map/dynamic_property_map.hpp: In member function 'void boost::detail::dynamic_property_map_adaptor<PropertyMap>::do_put(const boost::any&, const boost::any&, mpl_::bool_<true>)':  
/opt/wandbox/boost-1.70.0/gcc-head/include/boost/property_map/dynamic_property_map.hpp:150:25: error: no match for 'operator==' (operand types are 'const type_info' {aka 'const boost::typeindex::detail::ctti_data'} and 'const std::type_info')  
  150 |     if (in_value.type() == typeid(value_type)) {  
      |         ~~~~~~~~~~~~~~~ ^~ ~~~~~~~~~~~~~~~~~~  
      |                      |     |  
      |                      |     const std::type_info  
      |                      const type_info {aka const boost::typeindex::detail::ctti_data}  
  
The problem seems to be that typeid id used instead of boost::typeindex::type_id.
