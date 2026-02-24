# #59 - clang-tidy is reporting "Use of memory after it is freed" [Closed]

> Username: hg-zt  
> Created at: 2022-04-02 12:23:06 UTC  
> Updated at: 2022-04-14 18:03:52 UTC  
> Closed at: 2022-04-14 18:03:52 UTC  
> Url: https://github.com/boostorg/signals2/issues/59  

Hi, do you think it is misreporting? If it is I would like to suppress the warning  
  
```cpp  
#include <boost/signals2.hpp>  
  
int main()  
{  
  boost::signals2::signal<void ()> sig;  
  sig.connect([]{});  
  sig();  
}  
```  
  
The output of `clang-tidy`  
  
```bash  
❯ clang-tidy-14 --checks='clang-diag*' ../a.cpp  
1 warning generated.  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/smart_ptr/detail/shared_count.hpp:586:22: warning: Use of memory after it is freed [clang-analyzer-cplusplus.NewDelete]  
        if(pi_ != 0) pi_->weak_release();  
                     ^  
/tmp/foo/a.cpp:10:3: note: Calling 'signal::connect'  
  sig.connect([]{});  
  ^~~~~~~~~~~~~~~~~  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/signals2/detail/signal_template.hpp:691:16: note: Calling 'signal_impl::connect'  
        return (*_pimpl).connect(slot, position);  
               ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/signals2/detail/signal_template.hpp:164:18: note: Calling 'signal_impl::nolock_connect'  
          return nolock_connect(lock, slot, position);  
                 ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/signals2/detail/signal_template.hpp:552:14: note: 'position' is equal to at_back  
          if(position == at_back)  
             ^~~~~~~~  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/signals2/detail/signal_template.hpp:552:11: note: Taking true branch  
          if(position == at_back)  
          ^  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/signals2/detail/signal_template.hpp:562:29: note: Calling implicit destructor for 'weak_ptr<boost::signals2::detail::connection_body_base>'  
          return connection(newConnectionBody);  
                            ^  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/signals2/detail/signal_template.hpp:562:29: note: Calling '~weak_count'  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/smart_ptr/detail/shared_count.hpp:586:12: note: Field 'pi_' is not equal to null  
        if(pi_ != 0) pi_->weak_release();  
           ^  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/smart_ptr/detail/shared_count.hpp:586:9: note: Taking true branch  
        if(pi_ != 0) pi_->weak_release();  
        ^  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/smart_ptr/detail/shared_count.hpp:586:22: note: Calling 'sp_counted_base::weak_release'  
        if(pi_ != 0) pi_->weak_release();  
                     ^~~~~~~~~~~~~~~~~~~  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:132:13: note: Assuming the condition is true  
        if( atomic_decrement( &weak_count_ ) == 1 )  
            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:132:9: note: Taking true branch  
        if( atomic_decrement( &weak_count_ ) == 1 )  
        ^  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:134:13: note: Calling 'sp_counted_base::destroy'  
            destroy();  
            ^~~~~~~~~  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:99:9: note: Memory is released  
        delete this;  
        ^~~~~~~~~~~  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:134:13: note: Returning; memory was released  
            destroy();  
            ^~~~~~~~~  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/smart_ptr/detail/shared_count.hpp:586:22: note: Returning; memory was released  
        if(pi_ != 0) pi_->weak_release();  
                     ^~~~~~~~~~~~~~~~~~~  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/signals2/detail/signal_template.hpp:562:29: note: Returning from '~weak_count'  
          return connection(newConnectionBody);  
                            ^  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/signals2/detail/signal_template.hpp:562:29: note: Returning from destructor for 'weak_ptr<boost::signals2::detail::connection_body_base>'  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/signals2/detail/signal_template.hpp:164:18: note: Returning; memory was released  
          return nolock_connect(lock, slot, position);  
                 ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/signals2/detail/signal_template.hpp:691:16: note: Returning; memory was released  
        return (*_pimpl).connect(slot, position);  
               ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/tmp/foo/a.cpp:10:3: note: Returning; memory was released  
  sig.connect([]{});  
  ^~~~~~~~~~~~~~~~~  
/tmp/foo/a.cpp:10:19: note: Calling '~connection'  
  sig.connect([]{});  
                  ^  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/signals2/connection.hpp:265:22: note: Calling implicit destructor for 'weak_ptr<boost::signals2::detail::connection_body_base>'  
      ~connection() {}  
                     ^  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/signals2/connection.hpp:265:22: note: Calling '~weak_count'  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/smart_ptr/detail/shared_count.hpp:586:12: note: Field 'pi_' is not equal to null  
        if(pi_ != 0) pi_->weak_release();  
           ^  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/smart_ptr/detail/shared_count.hpp:586:9: note: Taking true branch  
        if(pi_ != 0) pi_->weak_release();  
        ^  
/home/hg/.conan/data/boost/1.78.0/_/_/package/cfef62e446e1bb07894de4523f2649afdfa94524/include/boost/smart_ptr/detail/shared_count.hpp:586:22: note: Use of memory after it is freed  
        if(pi_ != 0) pi_->weak_release();  
```

---

## Comment 1

> Username: fmhess  
> Created at: 2022-04-03 23:05:21 UTC  
> Url: https://github.com/boostorg/signals2/issues/59#issuecomment-1086968049  

It looks like it is coming from boost::weak_ptr have you tried reproducing  
this with just destroying a shared_ptr and then a weak_ptr that was  
referencing it?

---

## Comment 2

> Username: hg-zt  
> Created at: 2022-04-06 06:47:35 UTC  
> Updated at: 2022-04-06 06:53:13 UTC  
> Url: https://github.com/boostorg/signals2/issues/59#issuecomment-1089884641  

No warning in both cases.  
```cpp  
#include <boost/smart_ptr/weak_ptr.hpp>  
  
int main() {  
  {  
    auto s1 = boost::shared_ptr<int>{};  
    boost::weak_ptr<int> w1{s1};  
    s1.reset();  
  }  
  {  
    auto *s2 = new boost::shared_ptr<int>{};  
    boost::weak_ptr<int> w2{*s2};  
    delete s2;  
  }  
}  
```  
  
And I found there is no warning for the `signals2` example before boost/1.74  
w/o warning: 1.65, 1.73  
w/ warning: 1.74, 1.76, 1.77, 1.78  
  
But there is no change for `signals2` between 1.73 and 1.74 (`boost-1.73.0` and `boost-1.74.0` tag the same commit). Maybe it's an upstream issue for boost smart_ptr?

---

## Comment 3

> Username: fmhess  
> Created at: 2022-04-06 17:12:56 UTC  
> Url: https://github.com/boostorg/signals2/issues/59#issuecomment-1090508950  

What if the shared_ptr actually owns something before being assigned to the  
weak_ptr?  In your cases it is empty.

---

## Comment 4

> Username: fmhess  
> Created at: 2022-04-10 16:54:58 UTC  
> Url: https://github.com/boostorg/signals2/issues/59#issuecomment-1094323300  

I was able to reproduce the clang-tidy warning using your original example.  I think clang-tidy is just getting confused about when things actually get deleted.

---

## Comment 5

> Username: fmhess  
> Created at: 2022-04-14 18:03:52 UTC  
> Url: https://github.com/boostorg/signals2/issues/59#issuecomment-1099477249  

For the record, valgrind doesn't detect any memory errors when running the program clang-tidy complained about.
