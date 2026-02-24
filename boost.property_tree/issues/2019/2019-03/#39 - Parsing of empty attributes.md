# #39 - Parsing of empty attributes [Open]

> Username: zlomek  
> Created at: 2019-03-12 09:37:18 UTC  
> Updated at: 2019-03-12 09:37:18 UTC  
> Url: https://github.com/boostorg/property_tree/issues/39  

While parsing an empty attribute, function parse_node_attributes() correctly creates a new attribute with value of length 0 but it sets the pointer into the buffer instead of to nullptr.  
This does not harm in usual circumstances. But in case of cloning, it does. Then allocate_attribute() in clone_node() sets the value as value(val, internal::measure(val)) with length to the end of the buffer which is wrong.  
  
Could you please check?  
  
https://github.com/boostorg/property_tree/blob/29a7f0390b6904e9b7c447d1e6e44442bd0ab17b/include/boost/property_tree/detail/rapidxml.hpp#L2283
