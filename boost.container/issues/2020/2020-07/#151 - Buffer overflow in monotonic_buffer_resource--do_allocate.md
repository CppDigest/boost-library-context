# #151 - Buffer overflow in monotonic_buffer_resource::do_allocate [Closed]

> Username: Boronak  
> Created at: 2020-07-12 07:28:59 UTC  
> Updated at: 2020-07-12 09:08:44 UTC  
> Closed at: 2020-07-12 09:08:27 UTC  
> Url: https://github.com/boostorg/container/issues/151  

`remaining_storage` can set `aligner` to a number of bytes to be discarded which is then passed to `allocate_from_current`. However, if new memory is actually allocated this value is not reset. The bytes are discarded from the new buffer which may not be big enough to allow this.  
  
[aligner.txt](https://github.com/boostorg/container/files/4908166/aligner.txt)

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-07-12 09:08:44 UTC  
> Url: https://github.com/boostorg/container/issues/151#issuecomment-657194908  

Many thanks for the report!
