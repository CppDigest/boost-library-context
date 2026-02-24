# #64 - UBSan: runtime error: load of value 4294967295, which is not a valid value for type 'boost::interprocess::mode_t' [Closed]

> Username: ostash  
> Created at: 2018-09-26 07:43:04 UTC  
> Updated at: 2018-11-16 23:23:37 UTC  
> Closed at: 2018-11-16 23:23:37 UTC  
> Url: https://github.com/boostorg/interprocess/issues/64  

```  
    #0 0xbaf83a4 in void boost::interprocess::simple_swap<boost::interprocess::mode_t>(boost::interprocess::mode_t&, boost::interprocess::mode_t&) boost-1.67.0/include/boost/interprocess/detail/simple_swap.hpp:25:18  
    #1 0xbaf6f29 in boost::interprocess::ipcdetail::file_wrapper::swap(boost::interprocess::ipcdetail::file_wrapper&) boost-1.67.0/include/boost/interprocess/detail/file_wrapper.hpp:136:4  
    #2 0xbaf628f in void boost::interprocess::ipcdetail::managed_open_or_create_impl<boost::interprocess::ipcdetail::file_wrapper, 16ul, true, false>::priv_open_or_create<boost::interprocess::ipcdetail::create_open_func<boost::interprocess::ipcdetail::basic_managed_memory_impl<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index, 16ul> > >(boost::interprocess::ipcdetail::create_enum_t, char const* const&, unsigned long, boost::interprocess::mode_t, void const*, boost::interprocess::permissions const&, boost::interprocess::ipcdetail::create_open_func<boost::interprocess::ipcdetail::basic_managed_memory_impl<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index, 16ul> >) boost-1.67.0/include/boost/interprocess/detail/managed_open_or_create_impl.hpp:340:14  
    #3 0xbaf6133 in boost::interprocess::ipcdetail::managed_open_or_create_impl<boost::interprocess::ipcdetail::file_wrapper, 16ul, true, false>::managed_open_or_create_impl<boost::interprocess::ipcdetail::create_open_func<boost::interprocess::ipcdetail::basic_managed_memory_impl<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index, 16ul> > >(boost::interprocess::open_only_t, char const* const&, boost::interprocess::mode_t, void const*, boost::interprocess::ipcdetail::create_open_func<boost::interprocess::ipcdetail::basic_managed_memory_impl<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index, 16ul> > const&) boost-1.67.0/include/boost/interprocess/detail/managed_open_or_create_impl.hpp:201:7  
    #4 0xbaf3e24 in boost::interprocess::basic_managed_mapped_file<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, boost::interprocess::offset_ptr<void, long, unsigned long, 0ul>, 0ul>, boost::interprocess::iset_index>::basic_managed_mapped_file(boost::interprocess::open_only_t, char const*, void const*) boost-1.67.0/include/boost/interprocess/managed_mapped_file.hpp:120:9  
```  
  
If we take a look on  
```c++  
inline file_wrapper::file_wrapper()  
   :  m_handle(file_handle_t(ipcdetail::invalid_file()))  
{}  
```  
  
we can see that `m_mode` is not initialized.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2018-11-16 23:23:37 UTC  
> Url: https://github.com/boostorg/interprocess/issues/64#issuecomment-439558946  

Many thanks for the report.
