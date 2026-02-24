# #89 - How to use tiny pointer with boost::intrusive::rbtree? [Closed]

> Username: JaydenFish  
> Created at: 2025-02-17 15:11:22 UTC  
> Updated at: 2025-04-19 21:01:58 UTC  
> Closed at: 2025-04-19 21:01:56 UTC  
> Url: https://github.com/boostorg/intrusive/issues/89  

I need a custom boost::intrusive::rbtree whose node_ptr is uint32_t. All nodes are allocated from a array. So its node_ptr(pointer to left child, right child and parent) can be uint32_t but not void *. `uint32_t` is index of the array. So I can optimize memory use.  
How to do?  
Thanks very much.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-04-19 21:01:56 UTC  
> Url: https://github.com/boostorg/intrusive/issues/89#issuecomment-2816865365  

It's quite complicated.   
  
Not sure if your use case is similar to another one that was provided for testing (but not documented) in the library. See https://github.com/boostorg/intrusive/blob/develop/test/set_test.cpp, and analyze the test_main_template_bptr test. It only supports a global array and requires a lot of custom-made types (custom pointer type, custom reference type, allocator, etc.).  
  
I don't think it's worth the effort...
