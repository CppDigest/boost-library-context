# #100 - property_tree/ptree_serialization.hpp does not include boost/archive/basic_archive.hpp [Open]

> Username: urnathan  
> Created at: 2022-07-13 14:10:01 UTC  
> Updated at: 2023-11-19 17:00:23 UTC  
> Url: https://github.com/boostorg/property_tree/issues/100  

Here's the second clang-modules issue I found (with this and #99 addressed, I could build ptree_serialization as a submodule).  
  
It refers to some archive/basic_archive details.  
  
'''  
-- a/1.77.0/src/boost_1_77_0/boost/property_tree/ptree_serialization.hpp  
+++ b/1.77.0/src/boost_1_77_0/boost/property_tree/ptree_serialization.hpp  
@@ -10,8 +10,8 @@  
 #ifndef BOOST_PROPERTY_TREE_PTREE_SERIALIZATION_HPP_INCLUDED  
 #define BOOST_PROPERTY_TREE_PTREE_SERIALIZATION_HPP_INCLUDED  
   
+#include <boost/archive/basic_archive.hpp>  
 #include <boost/property_tree/ptree.hpp>  
-  
 #include <boost/serialization/nvp.hpp>  
 #include <boost/serialization/collections_save_imp.hpp>  
 #include <boost/serialization/detail/stack_constructor.hpp>  
'''

---

## Comment 1

> Username: ashtum  
> Created at: 2023-11-15 12:30:02 UTC  
> Url: https://github.com/boostorg/property_tree/issues/100#issuecomment-1812454105  

@urnathan, does this issue still exist?  
Looking at `ptree_serialization.hpp` I couldn't find any line that refers to archive/basic_archive.

---

## Comment 2

> Username: urnathan  
> Created at: 2023-11-18 23:35:53 UTC  
> Url: https://github.com/boostorg/property_tree/issues/100#issuecomment-1817677844  

@ashtum I'm not in a position to check any more, feel free to close the report,

---

## Comment 3

> Username: ashtum  
> Created at: 2023-11-19 05:38:43 UTC  
> Url: https://github.com/boostorg/property_tree/issues/100#issuecomment-1817754382  

Could you please provide some hints on what you were trying to accomplish? I might investigate it myself.

---

## Comment 4

> Username: urnathan  
> Created at: 2023-11-19 17:00:22 UTC  
> Url: https://github.com/boostorg/property_tree/issues/100#issuecomment-1817916165  

IIRC the issues I ran into were either   
a) header files not being 'self contained' -- ie, they relied on the includer having already included something they relied on.    
b) header files recursively including themselves -- unfortunately the standard CPP idiom doesn't detect this, as the idempotency macro is defined before the inner includes are included.  So the recursive include is silent unless of course one of the inner includes needs a declaration from the outer include.  
  
That breaks C++20 header-units and clang-modules was the approximation of that that was available to me.  Header units should have neither of those behaviours-- the header's sub-include graph should be complete and a DAG.  
  
But my memory's somewhat vague about this exact issue. Hope that helps though.
