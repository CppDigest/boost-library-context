# #58 - Is it safe/supported to use memory mapped file with STL containers? [Closed]

> Username: giumas  
> Created at: 2018-07-14 13:52:21 UTC  
> Updated at: 2018-07-24 00:12:31 UTC  
> Closed at: 2018-07-24 00:12:31 UTC  
> Url: https://github.com/boostorg/interprocess/issues/58  

I am having issues in using `boost::interprocess` to store the content of a huge `std::vector` on a memory mapped file. For instance, [it crashes in debug mode](https://stackoverflow.com/questions/51334785/read-access-violation-for-memory-mapped-vector-in-debug-mode).  
  
By reading the `boost::interprocess` documentation, it is not totally clear to me if I **must** adopt the provided `boost` STL-like containers, or I can also directly use STL containers for modern compilers (I mainly need to use MSVC14).   
  
Given that I use SWIG to bind my library on Python (3.6 thus MSVC14), it would be simpler for me to stick with the official STL containers (so that I can use the bundled SWIG interface files).

---

## Comment 1

> Username: sehe  
> Created at: 2018-07-14 15:49:37 UTC  
> Url: https://github.com/boostorg/interprocess/issues/58#issuecomment-405031848  

I'm pretty sure debug iterators are spoiling the party.  
  
Any standard library implementation that support stateful allocators with non-raw pointer types should be okay for use. In practice I expect all latest versions of mainstream compilers to match these requirements.   
  
 Boost Container library has some advantages though (zero-Alloc construction, more consistent support for scoped allocators)

---

## Comment 2

> Username: giumas  
> Created at: 2018-07-14 16:46:43 UTC  
> Url: https://github.com/boostorg/interprocess/issues/58#issuecomment-405035417  

Following @sehe's suggestion, I have [disabled debug iterators](https://msdn.microsoft.com/en-us/library/aa985982.aspx) by adding `#define _ITERATOR_DEBUG_LEVEL 0`. Unfortunately, this does not help to solve the issue.   
  
This is my current minimal example that crashes on MSVC14 in debug mode when it is executed more than once:  
```  
  
#include <boost/interprocess/managed_mapped_file.hpp>  
#include <iostream>  
  
#define _ITERATOR_DEBUG_LEVEL 0   
  
namespace bi = boost::interprocess;  
  
int main() {  
  std::string vecFile = "vector.dat";  
  bi::managed_mapped_file file_vec(bi::open_or_create, vecFile.c_str(), 10000000);  
  
  typedef bi::allocator<int, bi::managed_mapped_file::segment_manager> int_alloc;  
  typedef std::vector<int, int_alloc>  MyVec;  
  
  MyVec * vecptr = file_vec.find_or_construct<MyVec>("myvector")(file_vec.get_segment_manager());  
  
  vecptr->push_back(rand());  
  
  std::system("pause");  
  return 0;  
}  
```

---

## Comment 3

> Username: sehe  
> Created at: 2018-07-15 16:06:32 UTC  
> Updated at: 2018-07-15 16:15:35 UTC  
> Url: https://github.com/boostorg/interprocess/issues/58#issuecomment-405101271  

I've done the same, and documented the process. It does remove the issue. I wager you forgot an essential step (like removing the exiting file in between. I've also liberally increased the size of the memory mapped file, which might be a factor I didn't want to be interfering).  
  
## Test Results  
  
Creating a VM on azure just for the purpose, tested with the following slightly modified code to better understand the crash reasons:  
  
```cpp  
    #include <boost/interprocess/managed_mapped_file.hpp>  
    #include <iostream>  
      
    namespace bi = boost::interprocess;  
      
    int main() {  
        std::string vecFile = "vector.dat";  
        //std::remove(vecFile.c_str());  
        std::cout << __LINE__ << "\n";  
        {  
            bi::managed_mapped_file file_vec(bi::open_or_create, vecFile.c_str(), 100000);  
      
            typedef bi::allocator<int, bi::managed_mapped_file::segment_manager> int_alloc;  
            typedef std::vector<int, int_alloc>  MyVec;  
      
            MyVec * vecptr = file_vec.find_or_construct<MyVec>("myvector")(file_vec.get_segment_manager());  
      
            vecptr->push_back(rand());  
            std::cout << "size: " << vecptr->size() << "\n";  
        }  
        std::cout << __LINE__ << "\n";  
        {  
            bi::managed_mapped_file file_vec(bi::open_or_create, vecFile.c_str(), 100000);  
      
            typedef bi::allocator<int, bi::managed_mapped_file::segment_manager> int_alloc;  
            typedef std::vector<int, int_alloc>  MyVec;  
      
            MyVec * vecptr = file_vec.find_or_construct<MyVec>("myvector")(file_vec.get_segment_manager());  
      
            vecptr->push_back(rand());  
            std::cout << "size: " << vecptr->size() << "\n";  
        }  
        std::cout << __LINE__ << "\n";  
    }  
```  
  
Reproduces the issue. First run:  
  
[![enter image description here][1]][1]  
  
Subsequent run (with the `std::remove` line commented as shown):  
  
[![enter image description here][2]][2]  
  
## WORKAROUND DEMO  
  
After putting  
  
```cpp  
    #define _ITERATOR_DEBUG_LEVEL 0  
```  
  
at the very top **AND REMOVING THE `vector.dat` file because the change alters binary layout**:  
  
> Note: in your actual project you may require putting that `#define` in multiple translation units (especially consider `stdafx.cpp`). It's probably much better to include it in the project property sheets so it applies to all (future) translation units!  
  
[![enter image description here][3]][3]  
  
  
  [1]: https://i.stack.imgur.com/7C52M.png  
  [2]: https://i.stack.imgur.com/s6DYt.png  
  [3]: https://i.stack.imgur.com/Rj0By.png

---

## Comment 4

> Username: sehe  
> Created at: 2018-07-15 16:13:39 UTC  
> Url: https://github.com/boostorg/interprocess/issues/58#issuecomment-405101634  

@giumas you defined `_ITERATOR_DEBUG_LEVEL` **after** all the relevant includes. That's not how the preprocessor works.

---

## Comment 5

> Username: giumas  
> Created at: 2018-07-15 22:16:18 UTC  
> Updated at: 2018-07-15 22:18:00 UTC  
> Url: https://github.com/boostorg/interprocess/issues/58#issuecomment-405122618  

@sehe, it worked! That was a silly mistake on my side.  
  
While compiling the example code, did you notice several warnings like these?  
  
```  
2>  mmap_std_vector.cpp  
2>C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\include\xmemory0(586): warning C4494: 'std::allocator_traits<_Alloc>::allocate' : Ignoring __declspec(allocator) because the function return type is not a pointer or reference  
2>          with  
2>          [  
2>              _Alloc=boost::interprocess::allocator<int,boost::interprocess::segment_manager<char,boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family,boost::interprocess::offset_ptr<void,ptrdiff_t,uintptr_t,0>,0>,boost::interprocess::iset_index>>  
2>          ]  
2>  C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\include\xmemory0(887): note: see reference to class template instantiation 'std::allocator_traits<_Alloc>' being compiled  
2>          with  
2>          [  
2>              _Alloc=boost::interprocess::allocator<int,boost::interprocess::segment_manager<char,boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family,boost::interprocess::offset_ptr<void,ptrdiff_t,uintptr_t,0>,0>,boost::interprocess::iset_index>>  
2>          ]  
2>  C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\include\vector(450): note: see reference to class template instantiation 'std::_Wrap_alloc<boost::interprocess::allocator<int,boost::interprocess::segment_manager<CharType,MemoryAlgorithm,IndexType>>>' being compiled  
2>          with  
2>          [  
2>              CharType=char,  
2>              MemoryAlgorithm=boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family,boost::interprocess::offset_ptr<void,ptrdiff_t,uintptr_t,0>,0>,  
2>              IndexType=boost::interprocess::iset_index  
2>          ]  
2>  C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\include\vector(502): note: see reference to class template instantiation 'std::_Vec_base_types<_Ty,_Alloc>' being compiled  
2>          with  
2>          [  
2>              _Ty=int,  
2>              _Alloc=int_alloc  
2>          ]  
2>  C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\include\vector(680): note: see reference to class template instantiation 'std::_Vector_alloc<std::_Vec_base_types<_Ty,_Alloc>>' being compiled  
2>          with  
2>          [  
2>              _Ty=int,  
2>              _Alloc=int_alloc  
2>          ]  
2>  C:\code\examples\issues\mmap_std_vector.cpp(16): note: see reference to class template instantiation 'std::vector<int,int_alloc>' being compiled  
2>C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\include\xmemory0(592): warning C4494: 'std::allocator_traits<_Alloc>::allocate' : Ignoring __declspec(allocator) because the function return type is not a pointer or reference  
2>          with  
2>          [  
2>              _Alloc=boost::interprocess::allocator<int,boost::interprocess::segment_manager<char,boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family,boost::interprocess::offset_ptr<void,ptrdiff_t,uintptr_t,0>,0>,boost::interprocess::iset_index>>  
2>          ]  
2>C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\include\xmemory0(975): warning C4494: 'std::_Wrap_alloc<boost::interprocess::allocator<int,boost::interprocess::segment_manager<CharType,MemoryAlgorithm,IndexType>>>::allocate' : Ignoring __declspec(allocator) because the function return type is not a pointer or reference  
2>          with  
2>          [  
2>              CharType=char,  
2>              MemoryAlgorithm=boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family,boost::interprocess::offset_ptr<void,ptrdiff_t,uintptr_t,0>,0>,  
2>              IndexType=boost::interprocess::iset_index  
2>          ]  
2>C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\include\xmemory0(981): warning C4494: 'std::_Wrap_alloc<boost::interprocess::allocator<int,boost::interprocess::segment_manager<CharType,MemoryAlgorithm,IndexType>>>::allocate' : Ignoring __declspec(allocator) because the function return type is not a pointer or reference  
2>          with  
2>          [  
2>              CharType=char,  
2>              MemoryAlgorithm=boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family,boost::interprocess::offset_ptr<void,ptrdiff_t,uintptr_t,0>,0>,  
2>              IndexType=boost::interprocess::iset_index  
2>          ]  
```

---

## Comment 6

> Username: sehe  
> Created at: 2018-07-16 02:00:41 UTC  
> Updated at: 2018-07-16 02:01:51 UTC  
> Url: https://github.com/boostorg/interprocess/issues/58#issuecomment-405135268  

@giumas  I did not see those warnings. I **did** see them on RexTester though, so no doubt it's a standard library implementation version thing. (The Azure VM was running a version of VS2017, and I did not update to latest version of updates available)
