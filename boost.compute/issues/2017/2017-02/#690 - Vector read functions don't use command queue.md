# #690 - Vector read functions don't use command queue [Open]

> Username: Slonegg  
> Created at: 2017-02-13 22:12:05 UTC  
> Updated at: 2017-04-22 14:02:31 UTC  
> Url: https://github.com/boostorg/compute/issues/690  

I've noticed inconsistent behavior when reading values from vector using `.back()` routine. Digging it I've realized that essentially most of the vector read routines rely on the buffer_value class to read values from buffer and it uses some dummy command queue.  
```  
    operator value_type() const  
    {  
        if(m_buffer.get()){  
            const context &context = m_buffer.get_context();  
            const device &device = context.get_device();  
            command_queue queue(context, device);  
  
            return detail::read_single_value<T>(m_buffer, m_index / sizeof(T), queue);  
        }  
        else {  
            return m_value;  
        }  
}  
```  
  
I suggest to add command queue argument to read routines, this way you can ensure that operations on the buffer have been performed before actually reading values from it.

---

## Comment 1

> Username: jszuppe  
> Created at: 2017-04-20 19:47:35 UTC  
> Url: https://github.com/boostorg/compute/issues/690#issuecomment-295878097  

I guess there's nothing wrong in adding `front()`, `back()` and `at()` functions that take command queue argument.

---

## Comment 2

> Username: jszuppe  
> Created at: 2017-04-22 14:02:31 UTC  
> Url: https://github.com/boostorg/compute/issues/690#issuecomment-296375305  

After looking at the code, I think we can't do it without adding `command_queue` to `buffer_value` class.    
  
>I've noticed inconsistent behavior when reading values from vector using .back() routine.   
  
I think commit https://github.com/boostorg/compute/pull/710/commits/21ff71e2814c3c2e61e4bdc858ac6ecad030ab1d should fix your problem.
