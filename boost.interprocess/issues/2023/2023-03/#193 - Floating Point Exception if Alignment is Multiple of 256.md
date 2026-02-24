# #193 - Floating Point Exception if Alignment is Multiple of 256 [Closed]

> Username: michaelgranzow-avi  
> Created at: 2023-03-01 19:52:38 UTC  
> Updated at: 2026-01-07 09:58:19 UTC  
> Closed at: 2026-01-07 09:58:19 UTC  
> Url: https://github.com/boostorg/interprocess/issues/193  

The following code results in a floating point exception (division by zero) in construct():  
```  
#include <atomic>  
#include <sys/wait.h>  
  
#include "boost/interprocess/managed_shared_memory.hpp"  
  
using namespace boost::interprocess;  
  
class ShmHdr  
{  
public:  
    ShmHdr() : AllocCount(0) {}  
    std::atomic<unsigned long> AllocCount;  
} __attribute__((aligned (256)));  
  
int main(int argc, char const* argv[])  
{  
    fixed_managed_shared_memory ShmImpl(  
        open_or_create,  
        "MyFavoriteShm",  
        1024*1024,  
        reinterpret_cast<void *>(0x700000000000)  
    );  
  
    ShmHdr* Hdr = ShmImpl.construct<ShmHdr>("ShmStatsHeader")();  
  
    return 0;  
}  
```  
The reason seems to be that the variable that holds the alignment is of type unsigned char, which wraps back to zero if the value is 256.  
  
This is what it looks like in gdb (note that round_to is zero):  
```  
Program terminated with signal SIGFPE, Arithmetic exception.  
#0  0x000056108ed52e1e in boost::interprocess::ipcdetail::get_rounded_size<unsigned long> (orig_size=16, round_to=0) at /home/aviuser/boost/boost/interprocess/detail/utilities.hpp:57  
57            return ((orig_size-1)/round_to+1)*round_to;  
(gdb) where  
#0  0x000056108ed52e1e in boost::interprocess::ipcdetail::get_rounded_size<unsigned long> (orig_size=16, round_to=0) at /home/aviuser/boost/boost/interprocess/detail/utilities.hpp:57  
#1  0x000056108ed52c26 in boost::interprocess::ipcdetail::block_header<unsigned long>::value_offset (this=0x7ffcbb8ff7f0) at /home/aviuser/boost/boost/interprocess/detail/segment_manager_helper.hpp:153  
#2  0x000056108ed52bb5 in boost::interprocess::ipcdetail::block_header<unsigned long>::name_offset (this=0x7ffcbb8ff7f0) at /home/aviuser/boost/boost/interprocess/detail/segment_manager_helper.hpp:143  
#3  0x000056108ed5240c in boost::interprocess::ipcdetail::block_header<unsigned long>::total_size (this=0x7ffcbb8ff7f0) at /home/aviuser/boost/boost/interprocess/detail/segment_manager_helper.hpp:106  
#4  0x000056108ed526ff in boost::interprocess::ipcdetail::block_header<unsigned long>::total_size_with_header<boost::interprocess::ipcdetail::intrusive_value_type_impl<boost::intrusive::generic_hook<(boost::intrusive::algo_types)5, boost::intrusive::rbtree_node_traits<void*, true>, boost::intrusive::dft_tag, (boost::intrusive::link_mode_type)1, (boost::intrusive::base_hook_type)3>, char, unsigned long> > (this=0x7ffcbb8ff7f0) at /home/aviuser/boost/boost/interprocess/detail/segment_manager_helper.hpp:122  
#5  0x000056108ed518a0 in boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul>, boost::interprocess::iset_index>::priv_generic_named_construct<char> (this=0x700000000010, type=1 '\001',  
    name=0x56108ed5e44b "ShmStatsHeader", num=1, try2find=false, dothrow=true, table=..., index=..., is_intrusive=...) at /home/aviuser/boost/boost/interprocess/segment_manager.hpp:1143  
#6  0x000056108ed50934 in boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul>, boost::interprocess::iset_index>::priv_generic_construct (this=0x700000000010, name=0x56108ed5e44b "ShmStatsHeader", num=1,  
    try2find=false, dothrow=true, table=...) at /home/aviuser/boost/boost/interprocess/segment_manager.hpp:776  
#7  0x000056108ed5014d in boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul>, boost::interprocess::iset_index>::generic_construct<ShmHdr> (this=0x700000000010, name=0x56108ed5e44b "ShmStatsHeader", num=1,  
    try2find=false, dothrow=true, table=...) at /home/aviuser/boost/boost/interprocess/segment_manager.hpp:719  
#8  0x000056108ed4ff13 in boost::interprocess::ipcdetail::named_proxy<boost::interprocess::segment_manager<char, boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, void*, 0ul>, boost::interprocess::iset_index>, ShmHdr, false>::operator()<>() const (  
    this=0x7ffcbb8ff9d0) at /home/aviuser/boost/boost/interprocess/detail/named_proxy.hpp:132  
#9  0x000056108ed4d817 in main (argc=1, argv=0x7ffcbb8ffb28) at fpe.cc:24  
```  
Changing the alignment to 128 makes the crash go away.  
  
One obvious possible fix is to change the type of m_value_alignment in struct block_header to one that can hold larger values. Currently it is of type unsigned char:  
```  
template<class size_type>  
struct block_header  
{  
   size_type      m_value_bytes;  
   unsigned short m_num_char;  
   unsigned char  m_value_alignment;  
   unsigned char  m_alloc_type_sizeof_char;  
  
   block_header(size_type val_bytes  
// ...  
```  
Suggested diff:  
```  
diff --git a/include/boost/interprocess/detail/segment_manager_helper.hpp b/include/boost/interprocess/detail/segment_manager_helper.hpp  
index 036dab5..e3b9949 100644  
--- a/include/boost/interprocess/detail/segment_manager_helper.hpp  
+++ b/include/boost/interprocess/detail/segment_manager_helper.hpp  
@@ -81,7 +81,7 @@ struct block_header  
 {  
    size_type      m_value_bytes;  
    unsigned short m_num_char;  
-   unsigned char  m_value_alignment;  
+   unsigned short m_value_alignment;  
    unsigned char  m_alloc_type_sizeof_char;  
  
    block_header(size_type val_bytes  
@@ -92,7 +92,7 @@ struct block_header  
                )  
       :  m_value_bytes(val_bytes)  
       ,  m_num_char((unsigned short)num_char)  
-      ,  m_value_alignment((unsigned char)val_alignment)  
+      ,  m_value_alignment((unsigned short)val_alignment)  
       ,  m_alloc_type_sizeof_char( (unsigned char)((al_type << 5u) | ((unsigned char)szof_char & 0x1F)) )  
    {};  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2023-04-24 11:47:31 UTC  
> Url: https://github.com/boostorg/interprocess/issues/193#issuecomment-1519997167  

The proposed change breaks binary compatibility. So the sad news is that Boost.Interprocess does not support overaligned types bigger than 128 bytes.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2026-01-07 09:58:19 UTC  
> Url: https://github.com/boostorg/interprocess/issues/193#issuecomment-3718107319  

This was fixed in Boost 1.87, support for overaligned types was added, thanks for your report.
