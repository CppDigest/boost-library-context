# #248 - gcc-14 errors out on `segment_manager_helper.hpp`: `error: no matching function for call to 'get_rounded_size(const unsigned int:30&, const std::size_t&)'` [Closed]

> Username: barracuda156  
> Created at: 2025-02-02 13:27:04 UTC  
> Updated at: 2025-03-15 11:23:42 UTC  
> Closed at: 2025-03-02 22:23:58 UTC  
> Url: https://github.com/boostorg/interprocess/issues/248  

Details here: https://github.com/Bioconductor/BiocParallel/issues/265  
Like the issue raised by @eddelbuettel in https://github.com/boostorg/interprocess/issues/243 this also occurs with `R-BH` pulled in by BiocParallel.  
  
```  
:info:build /opt/local/bin/g++-mp-14 -std=gnu++11 -I"/opt/local/Library/Frameworks/R.framework/Resources/include" -DNDEBUG  -I'/opt/local/Library/Frameworks/R.framework/Versions/4.4/Resources/library/BH/include' -I'/opt/local/Library/Frameworks/R.framework/Versions/4.4/Resources/library/cpp11/include' -isystem/opt/local/include/LegacySupport -I/opt/local/include    -fPIC  -pipe -Os -arch ppc   -c cpp11.cpp -o cpp11.o  
:info:build /opt/local/bin/g++-mp-14 -std=gnu++11 -I"/opt/local/Library/Frameworks/R.framework/Resources/include" -DNDEBUG  -I'/opt/local/Library/Frameworks/R.framework/Versions/4.4/Resources/library/BH/include' -I'/opt/local/Library/Frameworks/R.framework/Versions/4.4/Resources/library/cpp11/include' -isystem/opt/local/include/LegacySupport -I/opt/local/include    -fPIC  -pipe -Os -arch ppc   -c ipcmutex.cpp -o ipcmutex.o  
:info:build In file included from /opt/local/Library/Frameworks/R.framework/Versions/4.4/Resources/library/BH/include/boost/interprocess/segment_manager.hpp:31,  
:info:build                  from /opt/local/Library/Frameworks/R.framework/Versions/4.4/Resources/library/BH/include/boost/interprocess/detail/managed_memory_impl.hpp:30,  
:info:build                  from /opt/local/Library/Frameworks/R.framework/Versions/4.4/Resources/library/BH/include/boost/interprocess/managed_shared_memory.hpp:25,  
:info:build                  from ipcmutex.cpp:14:  
:info:build /opt/local/Library/Frameworks/R.framework/Versions/4.4/Resources/library/BH/include/boost/interprocess/detail/segment_manager_helper.hpp: In instantiation of 'size_type boost::interprocess::ipcdetail::block_header<size_type>::name_length_offset() const [with size_type = unsigned int]':  
:info:build /opt/local/Library/Frameworks/R.framework/Versions/4.4/Resources/library/BH/include/boost/interprocess/detail/segment_manager_helper.hpp:379:52:   required from 'void boost::interprocess::ipcdetail::block_header<size_type>::store_name_length(name_len_t) [with size_type = unsigned int; name_len_t = short unsigned int]'  
:info:build   379 |       ::new( reinterpret_cast<char*>(this) + this->name_length_offset()  
:info:build       |                                              ~~~~~~^~~~~~~~~~~~~~~~~~  
:info:build /opt/local/Library/Frameworks/R.framework/Versions/4.4/Resources/library/BH/include/boost/interprocess/segment_manager.hpp:1083:29:   required from 'typename Proxy::object_type* boost::interprocess::segment_manager<CharType, MemoryAlgorithm, IndexType>::priv_generic_named_construct(Proxy, unsigned char, const CharT*, size_type, bool, bool, IndexType<boost::interprocess::ipcdetail::index_config<CharT, MemoryAlgorithm> >&) [with Proxy = boost::interprocess::ipcdetail::CtorArgN<boost::interprocess::interprocess_mutex, false>; CharT = char; CharType = char; MemoryAlgorithm = boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family>; IndexType = boost::interprocess::iset_index; typename Proxy::object_type = boost::interprocess::interprocess_mutex; size_type = unsigned int]'  
:info:build  1083 |       hdr->store_name_length(static_cast<typename block_header_t::name_len_t>(namelen));  
:info:build       |       ~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
:info:build /opt/local/Library/Frameworks/R.framework/Versions/4.4/Resources/library/BH/include/boost/interprocess/segment_manager.hpp:661:13:   required from 'typename Proxy::object_type* boost::interprocess::segment_manager<CharType, MemoryAlgorithm, IndexType>::generic_construct(Proxy&, const CharType*, size_type, bool, bool) [with Proxy = boost::interprocess::ipcdetail::CtorArgN<boost::interprocess::interprocess_mutex, false>; CharType = char; MemoryAlgorithm = boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family>; IndexType = boost::interprocess::iset_index; typename Proxy::object_type = boost::interprocess::interprocess_mutex; size_type = unsigned int]'  
:info:build   660 |          return this->priv_generic_named_construct  
:info:build       |                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
:info:build   661 |             (pr, unique_type, typeid(object_type).name(), num, try2find, dothrow, m_header.m_unique_index);  
:info:build       |             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
:info:build /opt/local/Library/Frameworks/R.framework/Versions/4.4/Resources/library/BH/include/boost/interprocess/detail/named_proxy.hpp:145:40:   required from 'T* boost::interprocess::ipcdetail::named_proxy<SegmentManager, T, is_iterator>::operator()(Args&& ...) const [with Args = {}; SegmentManager = boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family>, boost::interprocess::iset_index>; T = boost::interprocess::interprocess_mutex; bool is_iterator = false]'  
:info:build   145 |       return mp_mngr->generic_construct(ctor_obj, mp_name, m_num, m_find, m_dothrow);  
:info:build       |              ~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
:info:build ipcmutex.cpp:35:64:   required from here  
:info:build    35 |         mtx = shm->find_or_construct<interprocess_mutex>("mtx")();  
:info:build       |               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~  
:info:build /opt/local/Library/Frameworks/R.framework/Versions/4.4/Resources/library/BH/include/boost/interprocess/detail/segment_manager_helper.hpp:395:53: error: no matching function for call to 'get_rounded_size(const unsigned int:30&, const std::size_t&)'  
:info:build   395 |       return this->value_offset() + get_rounded_size(m_value_bytes, ::boost::move_detail::alignment_of<name_len_t>::value);  
:info:build       |                                     ~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
:info:build In file included from /opt/local/Library/Frameworks/R.framework/Versions/4.4/Resources/library/BH/include/boost/interprocess/detail/managed_memory_impl.hpp:26:  
:info:build /opt/local/Library/Frameworks/R.framework/Versions/4.4/Resources/library/BH/include/boost/interprocess/detail/utilities.hpp:54:17: note: candidate: 'template<class SizeType> SizeType boost::interprocess::ipcdetail::get_rounded_size(SizeType, SizeType)'  
:info:build    54 | inline SizeType get_rounded_size(SizeType orig_size, SizeType round_to)  
:info:build       |                 ^~~~~~~~~~~~~~~~  
:info:build /opt/local/Library/Frameworks/R.framework/Versions/4.4/Resources/library/BH/include/boost/interprocess/detail/utilities.hpp:54:17: note:   template argument deduction/substitution failed:  
:info:build /opt/local/Library/Frameworks/R.framework/Versions/4.4/Resources/library/BH/include/boost/interprocess/detail/segment_manager_helper.hpp:395:53: note:   deduced conflicting types for parameter 'SizeType' ('unsigned int' and 'long unsigned int')  
:info:build   395 |       return this->value_offset() + get_rounded_size(m_value_bytes, ::boost::move_detail::alignment_of<name_len_t>::value);  
:info:build       |                                     ~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
:info:build make: *** [ipcmutex.o] Error 1  
```  
  
I guess the breakage was introduced in https://github.com/boostorg/interprocess/commit/94bc4e11860d3be216d1c1c248c4cef000d2fc96 and apparently unfixed.  
  
@igaztanaga Could you please take a look? Any bugs in Boost are potentially a huge problem, since a lot of software uses it, including security-critical one.

---

## Comment 1

> Username: barracuda156  
> Created at: 2025-02-14 03:57:27 UTC  
> Url: https://github.com/boostorg/interprocess/issues/248#issuecomment-2658208938  

@k3DW @igaztanaga Could someone please take a look at this?

---

## Comment 2

> Username: barracuda156  
> Created at: 2025-03-02 20:11:24 UTC  
> Url: https://github.com/boostorg/interprocess/issues/248#issuecomment-2692885754  

@k3DW @igaztanaga @grafikrobot Any update on this?

---

## Comment 3

> Username: igaztanaga  
> Created at: 2025-03-02 22:24:37 UTC  
> Url: https://github.com/boostorg/interprocess/issues/248#issuecomment-2692939063  

Please let me know if the patch fixes your issue.

---

## Comment 4

> Username: barracuda156  
> Created at: 2025-03-11 11:29:01 UTC  
> Url: https://github.com/boostorg/interprocess/issues/248#issuecomment-2713791285  

Thank you! Sorry for a delay, travelling atm, will update on this soon.

---

## Comment 5

> Username: barracuda156  
> Created at: 2025-03-15 11:23:41 UTC  
> Url: https://github.com/boostorg/interprocess/issues/248#issuecomment-2726457850  

@igaztanaga I have confirmed that with your patch from https://github.com/boostorg/interprocess/commit/061bc6f8dfc2af90d9996a77317b21398f6bdda5 applied to `R-BH` (Boost headers for R), building `R-BiocParallel` works normally with no errors. Thank you!
