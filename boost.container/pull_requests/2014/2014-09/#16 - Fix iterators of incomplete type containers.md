# #16 Fix iterators of incomplete type containers [Merged]

> Username: mikael-s-persson  
> Created at: 2014-09-22 00:07:46 UTC  
> Updated at: 2014-09-22 20:33:15 UTC  
> Merged at: 2014-09-22 20:33:15 UTC  
> Closed at: 2014-09-22 20:33:15 UTC  
> Url: https://github.com/boostorg/container/pull/16  

Issue:  
The implementation of SCARY iterators from version 1.54 to 1.55 broke the ability to use iterators of containers of incomplete types. This was simply an unfortunate consequence of a slight short-cut taken in the SCARY iterator implementation. By invoking "it_value_type::value_type", where it_value_type is the "raw" value type of iterators, typically list_node, slist_node or tree_node, the compiler is required to instantiate these node class templates in order to retrieve the dependent type "value_type", and thus, it causes a compilation error when the actual value-type of the container is an incomplete type (making the node types incomplete as well).  
  
Fix:  
I did not manage to truly understand the ramifications of the iterator implementation under the hood. However, I came up with a very simple fix which replaces the it_value_type::value_type invocation by a simple meta-function iiterator_node_value_type, which is specialized for each node type (list_node, slist_node and tree_node). By adding iterators to the recursive container tests, it shows that it works (and it also works in other code that I have which requires this feature, which is why I wrote this fix!). Having iterators in those unit-tests should prevent future modifications from breaking this feature again.  
  
To Do:  
- Consider adopting a clearer guidelines in the documentation of the library about the guarantees for iterators of containers of incomplete types, instead of the vague statement that "all containers work for incomplete types". This feature is extremely important to Boost.Container, as it is one of the main motivations for using this library instead of STL implementation provided by the platform used.   
  
Commit:  
Fixes a small issue that prevented the use of iterators of containers of incomplete types.  
Added iterators of incomp-types to unit-tests.

---
