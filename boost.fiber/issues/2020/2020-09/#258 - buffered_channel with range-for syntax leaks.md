# #258 - buffered_channel with range-for syntax leaks [Closed]

> Username: dmitryikh  
> Created at: 2020-09-29 14:47:55 UTC  
> Updated at: 2021-09-20 17:02:06 UTC  
> Closed at: 2021-09-20 17:02:06 UTC  
> Url: https://github.com/boostorg/fiber/issues/258  

I'm storing std::shared_ptr inside buffered_channel:  
  
```  
using DataSPtr = std::shared_ptr<const Msg>;  
using Channel = boost::fibers::buffered_channel<DataSPtr>;  
```  
  
Then using range-for loop to consume values:  
```  
Channel ch(1024);  
...  
for (const auto msgSPtr : ch)  
{  
...  
}  
```  
  
This code leads to constantly memory leaks. The workaround with `while(true) & pop_value` works just fine.

---

## Comment 1

> Username: dmitryikh  
> Created at: 2020-09-29 14:48:52 UTC  
> Url: https://github.com/boostorg/fiber/issues/258#issuecomment-700757777  

It seems that increment_() never calls value's destructors:  
  
boost\fiber\buffered_channel.hpp:  
```cpp  
        void increment_() {  
            BOOST_ASSERT( nullptr != chan_);  
            try {  
                ::new ( static_cast< void * >( std::addressof( storage_) ) ) value_type{ chan_->value_pop() };  
            } catch ( fiber_error const&) {  
                chan_ = nullptr;  
            }  
        }  
```

---

## Comment 2

> Username: olk  
> Created at: 2020-09-30 05:43:16 UTC  
> Url: https://github.com/boostorg/fiber/issues/258#issuecomment-701168794  

Could you test the fix please?

---

## Comment 3

> Username: olk  
> Created at: 2021-09-20 17:02:06 UTC  
> Url: https://github.com/boostorg/fiber/issues/258#issuecomment-923109577  

no repsonse
