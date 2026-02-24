# #153 - boost::container::vector may incorrectly assume that the item type is trivially copyable [Closed]

> Username: ImplOfAnImpl  
> Created at: 2020-07-20 12:09:03 UTC  
> Updated at: 2020-10-30 12:20:36 UTC  
> Closed at: 2020-10-30 12:20:36 UTC  
> Url: https://github.com/boostorg/container/issues/153  

This should probably go to boost.move, but since the issue affects `boost::container::vector` badly, I'm reporting it here.  
  
So I've had this naive wrapper for `std::thread`:  
```  
class ThreadWrapper : public std::thread  
{  
public:  
    template <typename... Args>  
    ThreadWrapper(Args&&... args)  
        : std::thread(std::forward<Args>(args)...)  
    {  
    }  
  
    ThreadWrapper(const ThreadWrapper&) = delete;  
    ThreadWrapper& operator=(const ThreadWrapper&) = delete;  
      
    ThreadWrapper(ThreadWrapper&&) = default;  
    ThreadWrapper& operator=(ThreadWrapper&&) = default;  
  
    ~ThreadWrapper()  
    {  
        if (joinable())  
        {  
            join();  
        }  
    }  
};  
```  
I've had a vector of them and it all worked fine until I replaced std::vector with boost::container::vector. In short, the following code freezes when built with GCC (I've tried GCC 5.x, 6.x and 10):  
```  
std::atomic_bool done{false};  
const auto lambda = [done = std::ref(done)]()  
{  
    while (!done.get()) {}  
};  
  
boost::container::vector<ThreadWrapper> v;  
v.push_back(ThreadWrapper(lambda));  
v.push_back(ThreadWrapper(lambda));  
done = true;  
```  
What happens here is that boost.container checks whether the type is trivially copyable and, if so, performs a memcpy instead of the proper call to a ctor or assignment operator.  
The check is done via type trait classes from boost.move, which rely on certain compiler intrinsics, e.g.__has_trivial_copy. Those intrinsics don't always work as expected, e.g. __has_trivial_copy returns true for non-copyable types, so boost.move first checks that the type is copyable.  
But ThreadWrapper's ctor is too generic and it can be selected as a copy ctor with a non const argument. As a result, `boost::move_detail::is_trivially_copy_constructible<ThreadWrapper>::value` becomes true and `vector` starts memcpy'ing it. In case of libstdc++ this results in  `join()` being called too early.  
  
I've also come up with a more synthetic test, which doesn't depend on the flavor of the standard library being used:  
```  
#include <boost/container/vector.hpp>  
  
#include <functional>  
#include <iostream>  
  
struct Base  
{  
    Base(std::reference_wrapper<int> totalCounter, std::reference_wrapper<int> nonAssignableCounter):  
        totalCounter(&totalCounter.get()),  
        nonAssignableCounter(&nonAssignableCounter.get())  
    {  
        ++totalCounter;  
        ++nonAssignableCounter;  
    }  
  
    Base(Base&& other)  
    {  
        totalCounter = other.totalCounter;  
        nonAssignableCounter = other.nonAssignableCounter;  
  
        ++*totalCounter;  
        ++*nonAssignableCounter;  
    }  
  
    Base& operator=(Base&& other)  
    {  
        totalCounter = other.totalCounter;  
        return *this;  
    }  
  
    ~Base()  
    {  
        --*totalCounter;  
        --*nonAssignableCounter;  
    }  
  
    int* totalCounter = nullptr;  
    int* nonAssignableCounter = nullptr;  
};  
  
struct SemiCopyableTypeCtorConst: Base  
{  
    SemiCopyableTypeCtorConst(const SemiCopyableTypeCtorConst&) = delete;  
  
    template <typename... TT>  
    SemiCopyableTypeCtorConst(TT&&... tt): Base(std::move(tt)...)  
    {  
    }  
  
    template <typename... TT>  
    SemiCopyableTypeCtorConst& operator=(TT&&... tt)  
    {  
        Base::operator=(std::forward<TT>(tt)...);  
        return *this;  
    }  
};  
  
struct SemiCopyableTypeCtorNonConst: Base  
{  
    SemiCopyableTypeCtorNonConst(SemiCopyableTypeCtorNonConst&) = delete;  
  
    template <typename... TT>  
    SemiCopyableTypeCtorNonConst(TT&&... tt): Base(std::move(tt)...)  
    {  
    }  
  
    template <typename... TT>  
    SemiCopyableTypeCtorNonConst& operator=(TT&&... tt)  
    {  
        Base::operator=(std::forward<TT>(tt)...);  
        return *this;  
    }  
};  
  
struct SemiCopyableTypeAssignmentConst: Base  
{  
    SemiCopyableTypeAssignmentConst& operator=(const SemiCopyableTypeAssignmentConst&) = delete;  
  
    template <typename... TT>  
    SemiCopyableTypeAssignmentConst(TT&&... tt): Base(std::move(tt)...)  
    {  
    }  
  
    template <typename... TT>  
    SemiCopyableTypeAssignmentConst& operator=(TT&&... tt)  
    {  
        Base::operator=(std::forward<TT>(tt)...);  
        return *this;  
    }  
};  
  
struct SemiCopyableTypeAssignmentNonConst: Base  
{  
    SemiCopyableTypeAssignmentNonConst& operator=(SemiCopyableTypeAssignmentNonConst&) = delete;  
  
    template <typename... TT>  
    SemiCopyableTypeAssignmentNonConst(TT&&... tt): Base(std::move(tt)...)  
    {  
    }  
  
    template <typename... TT>  
    SemiCopyableTypeAssignmentNonConst& operator=(TT&&... tt)  
    {  
        Base::operator=(std::forward<TT>(tt)...);  
        return *this;  
    }  
};  
  
template <typename SemiCopyableType>  
void test()  
{  
    constexpr int Count = 10;  
      
    int totalCounter = 0;  
    int nonAssignableCounters[Count] = {};  
      
    {  
        boost::container::vector<SemiCopyableType> vv;  
          
        for (int i = 0; i < Count; ++i)  
        {  
            vv.push_back(SemiCopyableType(std::ref(totalCounter), std::ref(nonAssignableCounters[i])));  
        }  
          
        for (int i = 0; i < Count; ++i)  
        {  
            vv.erase(vv.begin());  
        }  
    }  
      
    std::cout << "totalCounter = " << totalCounter << "; ";  
    std::cout << "nonAssignableCounters = {";  
      
    for (int i = 0; i < Count; ++i)  
    {  
        std::cout << nonAssignableCounters[i];  
          
        if (i != Count - 1)  
        {  
            std::cout << ", ";  
        }  
    }  
      
    std::cout << "}" << std::endl;  
}  
  
int main()  
{  
    std::cout << "SemiCopyableTypeCtorConst:";  
    test<SemiCopyableTypeCtorConst>();  
    std::cout << "SemiCopyableTypeCtorNonConst: ";  
    test<SemiCopyableTypeCtorNonConst>();  
    std::cout << "SemiCopyableTypeAssignmentConst:";  
    test<SemiCopyableTypeAssignmentConst>();  
    std::cout << "SemiCopyableTypeAssignmentNonConst: ";  
    test<SemiCopyableTypeAssignmentNonConst>();  
}  
  
```  
Here all counters should be zero but they are not:  
```  
$ clang++-9.0 foo.cpp -o test -isystem ~/Downloads/boost_1_73_0_orig/ -std=c++14 -stdlib=libc++ && ./test  
SemiCopyableTypeCtorConst:totalCounter = -25; nonAssignableCounters = {-6, -5, -4, -3, -2, -2, -1, -1, -1, 0}  
SemiCopyableTypeCtorNonConst: totalCounter = 0; nonAssignableCounters = {0, 0, 0, 0, 0, 0, 0, 0, 0, 0}  
SemiCopyableTypeAssignmentConst:totalCounter = -25; nonAssignableCounters = {-6, -5, -4, -3, -2, -2, -1, -1, -1, 0}  
SemiCopyableTypeAssignmentNonConst: totalCounter = -25; nonAssignableCounters = {-6, -5, -4, -3, -2, -2, -1, -1, -1, 0}  
```  
  
P.S. I've solved the problem locally by patching boost/move/detail/type_traits.hpp as follows:  
```  
--- boost/move/detail/type_traits.hpp  
+++ boost/move/detail/type_traits.hpp  
@@ -24,6 +24,9 @@  
 #  pragma once  
 #endif  
   
+#include <boost/type_traits/is_assignable.hpp>  
+#include <boost/type_traits/is_constructible.hpp>  
+  
 #include <boost/move/detail/config_begin.hpp>  
 #include <boost/move/detail/workaround.hpp>  
   
@@ -236,7 +239,8 @@  
   
 #ifdef BOOST_MOVE_HAS_TRIVIAL_COPY  
    #define BOOST_MOVE_IS_TRIVIALLY_COPY_CONSTRUCTIBLE(T)   ::boost::move_detail::is_pod<T>::value ||\  
-                                                          (::boost::move_detail::is_copy_constructible<T>::value &&\  
+                                                          (::boost::is_constructible<T, T&>::value &&\  
+                                                           ::boost::is_constructible<T, const T&>::value &&\  
                                                            BOOST_MOVE_HAS_TRIVIAL_COPY(T))  
 #else  
    #define BOOST_MOVE_IS_TRIVIALLY_COPY_CONSTRUCTIBLE(T)   ::boost::move_detail::is_pod<T>::value  
@@ -256,7 +260,8 @@  
   
 #ifdef BOOST_MOVE_HAS_TRIVIAL_ASSIGN  
    #define BOOST_MOVE_IS_TRIVIALLY_COPY_ASSIGNABLE(T) ::boost::move_detail::is_pod<T>::value ||\  
-                                                      ( ::boost::move_detail::is_copy_assignable<T>::value &&\  
+                                                      ( ::boost::is_assignable<T, T&>::value &&\  
+                                                        ::boost::is_assignable<T, const T&>::value &&\  
                                                          BOOST_MOVE_HAS_TRIVIAL_ASSIGN(T))  
 #else  
    #define BOOST_MOVE_IS_TRIVIALLY_COPY_ASSIGNABLE(T) ::boost::move_detail::is_pod<T>::value  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-10-30 12:20:36 UTC  
> Url: https://github.com/boostorg/container/issues/153#issuecomment-719520814  

I've checked that using boost::has_trivial_copy instead of Boost.Move's version does not fix the issue, because TypeTraits uses the same intrinsics as Boost.Move. The alternative is to use the new intrinsics like "__is_trvially_copy_constructible", at least for Clang and GCC. The following commit adapts Boost.Move to use the new intrinsics:  
  
https://github.com/boostorg/move/commit/eb941db76cf8fb24742570f5ab1551b49adb8faa
