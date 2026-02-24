# #389 - io_uring write_some_at writes at wrong offset [Open]

> Username: JoelKatz  
> Created at: 2022-03-05 23:32:48 UTC  
> Updated at: 2022-03-16 11:29:23 UTC  
> Url: https://github.com/boostorg/asio/issues/389  

In `io_uring_descriptor_service.hpp` we have:  
  
          return descriptor_ops::sync_write_at1(impl.descriptor_,  
              offset, impl.state_, bufs_type::first(buffers).data(),  
              bufs_type::first(buffers).size(), ec);  
  
But in `descriptor_ops.ipp` we have:  
  
    std::size_t sync_write_at1(int d, state_type state, uint64_t offset,  
        const void* data, std::size_t size, boost::system::error_code& ec)  
  
The observed symptom is that writes using `write_some_at` write to a constant and incorrect offset. Reversing the `offset` and `state` parameters in `io_uring_descriptor_service.hpp` fixes the symptom. I'm not confident that's the correct fix though and this same issue may be present in related code paths.

---

## Comment 1

> Username: ljhaoge  
> Created at: 2022-03-16 11:29:11 UTC  
> Url: https://github.com/boostorg/asio/issues/389#issuecomment-1069024677  

Excuse me (t.radarlab.org) When can I log in
