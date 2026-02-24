# #76 - Error static_cast [Closed]

> Username: Swierzewski  
> Created at: 2024-11-05 10:04:11 UTC  
> Updated at: 2024-11-06 10:47:59 UTC  
> Closed at: 2024-11-06 10:47:58 UTC  
> Url: https://github.com/boostorg/multi_index/issues/76  

Hello, I have the following error in Visual Studio 2022 with C++20  (it compiles with C++17):   
  
1>boost\multi_index\detail\node_handle.hpp(155,12): error C2760: syntax error: 'static_cast' was unexpected here; expected 'type specifier'  
1>(compiling source file 'configs/file.cxx')  
1>    boost\multi_index\detail\node_handle.hpp(155,12):  
1>    the template instantiation context (the oldest one first) is  
1>        boost\multi_index\detail\node_handle.hpp(46,7):  
1>        while compiling class template 'boost::multi_index::detail::node_handle'  
  
The line is 155 is the following :   
private:  
  BOOST_MOVABLE_BUT_NOT_COPYABLE(node_handle)  
  
  template <typename,typename,typename>  
  friend class boost::multi_index::multi_index_container;  
  
  node_handle(Node* node_,const allocator_type& al):node(node_)  
  {  
    ::new (static_cast<void*>(allocator_ptr())) allocator_type(al);  
  }  
  
Someone already had a similar error and can help me ?

---

## Comment 1

> Username: Swierzewski  
> Created at: 2024-11-05 14:48:17 UTC  
> Url: https://github.com/boostorg/multi_index/issues/76#issuecomment-2457402485  

The version of the boost is 1.86 and of the VS2022 is 17.11.2

---

## Comment 2

> Username: joaquintides  
> Created at: 2024-11-06 10:43:40 UTC  
> Url: https://github.com/boostorg/multi_index/issues/76#issuecomment-2459405796  

Hi @Swierzewski, I can't seem to reproduce any such problem locally (VS 2022 Version 17.2.3). Can you provide a minimal reproducible example of the error? Unfortunately, Compiler Explorer seems not to be able to include Boost headers currently, so I'm afraid well have to make do with an offline code snippet, I'll see how I can locate an appropriate VS version to try it out.  
  
Thank you,

---

## Comment 3

> Username: Swierzewski  
> Created at: 2024-11-06 10:47:58 UTC  
> Url: https://github.com/boostorg/multi_index/issues/76#issuecomment-2459416984  

Thank you, we just solve the problem. It was an easy one actually. The afx library was included first before the boost and that caused the problem.   
  
Thank you.
