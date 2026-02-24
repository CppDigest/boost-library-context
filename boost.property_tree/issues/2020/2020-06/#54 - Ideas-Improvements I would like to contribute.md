# #54 - Ideas/Improvements I would like to contribute [Open]

> Username: yost-jacob  
> Created at: 2020-06-30 11:52:23 UTC  
> Updated at: 2020-06-30 12:12:25 UTC  
> Url: https://github.com/boostorg/property_tree/issues/54  

If there is interest I would like to contribute the following:  
  
- change m_children to be a boost/std any instead of a void* -  We can the default the move and copy constructors assignment operators, and pretty sure the destructor.  It at least works on the version I have been playing with.  
  
- implement rvalue references (issue:42 ) -  Its mostly pretty straightforward overloads, except for where we are deducing Type, in these cases we need to add an enable_if<is_rvalue_reference<Type&&> ,Type> or similar to prevent the universal forwarding references (Type&&) from being too greedy.  
  
- pass path by value - a copy is made immediately in most cases anyway  
  
- add a string_view constructor to string_path  
  
- use other boost config helper macros to do things like add const or constexpr and noexcept where appropriate  
- use macros to use std library versions of type traits, any, optional, string_view as appropriate.  
  
- create a boost::lexical_cast based translator instead of stringstream.  maybe make a macro flag to switch between them.  
  
- find other opportunities to use string_view  
  
- then in the far off future, a YAML parser.  
  
- [[unlikely]] i may update the unit testing.  i think its set up to use the minimal implementation of test which is deprecated and took a lot of fiddling with  
  
If there is any real interest, I can work on the pull requests for review.
