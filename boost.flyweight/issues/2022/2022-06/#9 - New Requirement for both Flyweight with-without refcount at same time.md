# #9 - New Requirement for both Flyweight with/without refcount at same time [Closed]

> Username: akeyliu  
> Created at: 2022-06-21 15:18:31 UTC  
> Updated at: 2022-07-24 03:00:01 UTC  
> Closed at: 2022-07-24 03:00:01 UTC  
> Url: https://github.com/boostorg/flyweight/issues/9  

Hello,    
    Thanks for your contribution to Boost.Flyweight.  
  
     I've some new requirements for with/without refcount in the same Flyweight.  
     Here is the situation:  
    1. There are many of data (i.e. Integers ) that are static, i.e. some parameters or imported initial data, which will not change anymore.  
    2.  There are also many data that are dynamic changed, and scope may duplicate with previous static data.  
    3.  To consider efficiency and memory usage at the same time:   
        (1) Static Flyweight is no_tracking, while Dynamic Flyweight is with refcount.  
        (2) If a static flyweight value already exists, then return the static flyweight reference;   
        (3) With a flag for static or dynamic, the static flag will add a static flyweight value, else add a dynamic flyweight value and return..  
        (4) when re-assign Flyweight variable, only check dynamic Flyweight and decrease refcount if exists.  
  
   Hope you can consider this requirement, and appreciate your response and answer.  
    
  Thanks!  
  
Kevin Liu

---

## Comment 1

> Username: joaquintides  
> Created at: 2022-06-29 07:35:50 UTC  
> Updated at: 2022-06-29 09:43:42 UTC  
> Url: https://github.com/boostorg/flyweight/issues/9#issuecomment-1169637429  

Hi Kevin,  
  
I consider the scenario too specific to cover it natively from Boost.Flyweight, but you can support it with a user-provided tracking policy (C++17 below, can be backported to other versions of C++):  
  
```cpp  
/* Copyright 2022 Joaquin M Lopez Munoz.  
 * Distributed under the Boost Software License, Version 1.0.  
 * (See accompanying file LICENSE_1_0.txt or copy at  
 * http://www.boost.org/LICENSE_1_0.txt)  
 */  
  
#include <boost/container_hash/hash_fwd.hpp>  
#include <boost/detail/atomic_count.hpp>  
#include <boost/flyweight/tracking_tag.hpp>  
#include <utility>  
  
struct dont_track_t{};  
inline dont_track_t dont_track;  
  
template<typename T>  
class optional_tracked  
{  
public:  
  template<typename... Args>  
  explicit optional_tracked(Args&&... args):  
    x(std::forward<Args>(args)...),tracked_(true){}  
  template<typename... Args>  
  explicit optional_tracked(dont_track_t&,Args&&... args):  
    x(std::forward<Args>(args)...),tracked_(false){}  
      
  bool tracked()const{return tracked_;}  
    
  operator T& (){return x;}  
  operator const T& ()const{return x;}  
    
  friend bool operator==(const optional_tracked& x,const optional_tracked& y)  
  {  
    return static_cast<const T&>(x)==static_cast<const T&>(y);   
  }  
    
private:  
  T    x;  
  bool tracked_;  
};  
  
template<typename T>  
std::size_t hash_value(const optional_tracked<T>& x){return boost::hash<T>()(x);}  
  
template<typename Value,typename Key>  
class optional_refcounted_entry  
{  
public:  
  explicit optional_refcounted_entry(const Value& x):x(x){}  
  explicit optional_refcounted_entry(Value&& x):x(std::move(x)){}    
  optional_refcounted_entry(const optional_refcounted_entry& e):x(e.x){}  
  optional_refcounted_entry(optional_refcounted_entry&& e):x(std::move(e.x)){}    
  
  operator const Value&()const{return x;}  
  operator const Key&()const{return x;}  
      
private:  
  template<typename,typename> friend class optional_refcounted_handle;  
  
  long count()const{return ref;}  
  long add_ref()const{return ++ref;}  
  bool release()const{return (--ref==0);}  
  
  void add_deleter()const{++del_ref;}  
  bool release_deleter()const{return (--del_ref==0);}  
  
  Value                               x;  
  mutable boost::detail::atomic_count ref{!static_cast<const Key&>(x).tracked()};  
  mutable long                        del_ref=0;  
};  
  
template<typename Handle,typename TrackingHelper>  
class optional_refcounted_handle  
{  
public:  
  explicit optional_refcounted_handle(const Handle& h):h(h)  
  {  
    if(TrackingHelper::entry(*this).add_ref()==1){  
      TrackingHelper::entry(*this).add_deleter();  
    }  
  }  
    
  optional_refcounted_handle(const optional_refcounted_handle& x):h(x.h)  
  {  
    TrackingHelper::entry(*this).add_ref();  
  }  
  
  optional_refcounted_handle& operator=(optional_refcounted_handle x)  
  {  
    std::swap(h,x.h);  
    return *this;  
  }  
  
  ~optional_refcounted_handle()  
  {  
    if(TrackingHelper::entry(*this).release()){  
      TrackingHelper::erase(*this,check_erase);  
    }  
  }  
  
  operator const Handle&()const{return h;}  
    
private:  
  static bool check_erase(const optional_refcounted_handle& x)  
  {  
    return TrackingHelper::entry(x).release_deleter();  
  }  
  
  Handle h;  
};  
  
struct optional_refcounted:boost::flyweights::tracking_marker  
{  
  struct entry_type  
  {  
    template<typename Value,typename Key>  
    struct apply  
    {  
      using type=optional_refcounted_entry<Value,Key>;  
    };  
  };  
  
  struct handle_type  
  {  
    template<typename Handle,typename TrackingHelper>  
    struct apply  
    {  
      using type=optional_refcounted_handle<Handle,TrackingHelper>;  
    };  
  };  
};  
  
// testing  
  
#include <boost/flyweight.hpp>  
#include <cassert>  
  
using flyweight_t=boost::flyweight<  
  optional_tracked<int>,optional_refcounted>;  
    
int main()  
{  
  flyweight_t x(0);  
  assert(x.get().tracked());  
    
  flyweight_t y(dont_track,0);  
  assert(y.get().tracked());  
    
  flyweight_t z(dont_track,1);  
  assert(!z.get().tracked());  
    
  x=z;  
  assert(x.get()==1);  
  assert(!x.get().tracked());  
    
  {  
    flyweight_t w(2);  
    assert(w.get().tracked());  
  }  
    
  flyweight_t w(dont_track,2);  
  assert(!w.get().tracked());  
    
  {  
    flyweight_t v(dont_track,3);  
    assert(!v.get().tracked());  
  }  
    
  flyweight_t v(3);  
  assert(!v.get().tracked());  
}  
```  
The idea is to have an `optional_tracked<T>` holding a data member indicating whether the element should be tracked or not, and then a tracking policy that inspects that member and, when the element is not to be tracked, bumps its reference count to 1 so as to guarantee it won't ever be evicted from the factory.  
  
Hope this helps,

---

## Comment 2

> Username: akeyliu  
> Created at: 2022-07-07 15:21:46 UTC  
> Url: https://github.com/boostorg/flyweight/issues/9#issuecomment-1177783087  

Hello,   
      Really appreciate your support and advice and I will try the code first.  
      In my opinion that I will envelop one flyweight with no_tracking and no_counting, and another flyweight with simple_locking and refcount as two private variables, and develop an interface template to provide operate function like a normal flyweight with another parameter of bTraceFlag whose default is false.  
     This design will reuse the boost flyweight library with minimal code for the new function.  
  
     Thanks again.  
  
Kevin Liu

---

## Comment 3

> Username: akeyliu  
> Created at: 2022-07-07 15:29:43 UTC  
> Updated at: 2022-07-10 14:42:00 UTC  
> Url: https://github.com/boostorg/flyweight/issues/9#issuecomment-1177795934  

As another question related but not so link close, I write a new comment for it.  
(1) With C++ 17 and Nvidia HPC library, nvcc can compile c++17 code in GPU with HPC library. Vector is suggested to be used in the code for GPU with the support of HPC and compiled to run on multi-core CPU with OpenMP support.  
(2）The boost library provides flat_map, and flat_set to support map/set with the format of the vector.  
So, my question is, if vector<boost::flyweight<T> > can be applied in GPU with the HPC library?  
  
PS:  https://on-demand.gputechconf.com/supercomputing/2019/pdf/sc1936-gpu-programming-with-standard-c++17.pdf  
This PDF describle how to use GPU/Multi-core CPU in C++ 17 with execution policy which can only switch with the compile flag, and no need to change the source code.

---

## Comment 4

> Username: joaquintides  
> Created at: 2022-07-11 08:33:03 UTC  
> Url: https://github.com/boostorg/flyweight/issues/9#issuecomment-1180116708  

I'm not familiar with GPU programming, but from the refeference you provide I doubt a vector of flyweights will be accepted by HPC or similar libraries --flyweights are ultimately pointers to CPU memory.  
  
Anyway, have you tried compiling your stuff with GPU switches? What did the compiler say?

---

## Comment 5

> Username: akeyliu  
> Created at: 2022-07-12 10:11:38 UTC  
> Updated at: 2022-07-12 10:21:35 UTC  
> Url: https://github.com/boostorg/flyweight/issues/9#issuecomment-1181577074  

1. Yes, I'm now studying to compile and run c++ code with GPU, There are many methods to run c++ in GPU.    
2. If limited to pure c++, a few methods can be selected, including HPC (Nvidia), OpenMP, and OpenACC.   
3. The HPC method has advantages that no need to change any c++ source code (need c++17 and follow some other constraints for function and variable memory access). And GCC also can compile the c++ 17 parallel policy in Multi-core CPU using OpenMP and Intel TBB. I installed the last version of Nvidia HPC, but there is no sample code and Compile error using the method in the Previous PDF. ( I guess maybe it is the problem of license or other copyright problem). So, I am still waiting for the updated version of nvcc from Nvidia.  
4.  The OpenMP and OpenACC are the backup method for running the c++ code in GPU, which need to change the source code. and which is tested in nvcc from Nvidia.  
5.  No matter which kind of method, the vector is used for data in GPU, and boost also provides flat_map and flat_set for similar vector containers.    
6. That's why I'm interested in boost::flyweight for GPU coding.  
  
PS:  For the with/without refcount/locking in Flyweight, I cannot find a method to check if the data is in the Flyweight factory or not ( it seems only insert, erase and swap functions are provided), And I hope the check function can be added.
