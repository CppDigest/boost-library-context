# #74 - Move constructor of list hook is missing [Open]

> Username: gaohuazuo  
> Created at: 2022-01-12 12:31:46 UTC  
> Updated at: 2022-01-13 06:32:59 UTC  
> Url: https://github.com/boostorg/intrusive/issues/74  

Currently, list hooks do not declare a move constructor. When moved, the copy constructor is called instead, resulting the moved-from hook still linked and the moved-to hook unlinked (https://godbolt.org/z/zKqqhbxz5). An consequence is that objects containing list hooks cannot be safely put inside some STL containers like `std::vector` (https://godbolt.org/z/vq765G5n5). Furthermore, it is not alway possible to implement a desired move constructor for the class containing list hooks, since link / unlink cannot be performed without the list object if the list has constant time `size()`.  
  
I think making the following changes to list hooks would make them safer and more broadly useful:  
  
* declare the move constructor as deleted  
* enabled with an option, add a move constructor that unlinks the moved-from hook and link the moved-to hook in its place
