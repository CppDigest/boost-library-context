# #79 - a new lock-free queue [Open]

> Username: inie0722  
> Created at: 2022-06-09 03:08:02 UTC  
> Updated at: 2022-06-29 08:35:06 UTC  
> Url: https://github.com/boostorg/lockfree/issues/79  

In theory, it is a queue without waiting, which supports multi-threaded reading and writing. Of course, the disadvantage is that the order of entering the queue may not be strongly ordered.  
  
```cpp  
template<typename T, size_t N>  
class queue  
{  
private:  
    ring_buffer<T, N> value_;  
  
    ring_buffer<atomic<size_t>, N> readable_flag_{0};  
    ring_buffer<atomic<size_t>, N> writable_flag_{0};  
  
    atomic<size_t> writable_limit_;  
    atomic<size_t> readable_limit_;  
public:  
    queue() = default;  
    ~queue() = default;  
  
    void push(const T & val)  
    {  
        size_t index = writable_limit_.fetch_add(1);  
  
        while (writable_flag_[index] != index / max_size())  
            ;  
          
        value_[index] = val;  
        readable_flag_[index] = (index / max_size()) + 1;  
    }  
  
    void pop(T&val)  
    {  
        size_t index = readable_limit_.fetch_add(1);  
  
        while(readable_flag_[index] != (index / max_size()) + 1)  
            ;  
  
        val = value_[index];  
        writable_flag_[index] = (index / max_size()) + 1;  
    }  
  
    size_t max_size() const  
    {  
        return N;  
    }  
  
    size_t size() const  
    {  
        size_t writable_limit = writable_limit_;  
        size_t readable_limit = readable_limit_;  
  
        return writable_limit > readable_limit ? writable_limit - readable_limit : 0;  
    }  
};  
```

---

## Comment 1

> Username: inie0722  
> Created at: 2022-06-29 08:35:06 UTC  
> Url: https://github.com/boostorg/lockfree/issues/79#issuecomment-1169692653  

c language implementation  
[mpmc_queue.h](https://github.com/inie0722/CTL/blob/master/include/CTL/lockfree/mpmc_queue.h)  
[mpmc_queue.c](https://github.com/inie0722/CTL/blob/master/src/lockfree/mpmc_queue.c)
