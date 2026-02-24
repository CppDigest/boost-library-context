# #113 - ringbuffer_base<T>::reset  Fails to Compile Due to Missing Parameters [Open]

> Username: JohnSmithGeek-coder  
> Created at: 2025-03-18 07:15:41 UTC  
> Updated at: 2025-03-26 12:27:34 UTC  
> Url: https://github.com/boostorg/lockfree/issues/113  

The reset() function in ringbuffer_base:  
​Compilation Failure: Calls consume_all with missing parameters (internal_buffer and max_size).  
  
```c++  
void reset(void) {  
    if (!std::is_trivially_destructible<T>::value) {  
        consume_all([](const T&) {}); // Missing `buffer` and `max_size`!  
    } else {  
        write_index_.store(0, memory_order_relaxed);  
        read_index_.store(0, memory_order_release);  
    }  
}  
```

---

## Comment 1

> Username: timblechmann  
> Created at: 2025-03-26 12:27:33 UTC  
> Url: https://github.com/boostorg/lockfree/issues/113#issuecomment-2754236142  

i cannot reproduce it with. please provide a reduced test case, include the full command line including compiler version and make sure it's reproducible with `master`
