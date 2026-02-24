# #671 - rename compute_queue::enqueue_unmap_buffer to enqueue_unmap_buffer_async [Closed]

> Username: Ulfgard  
> Created at: 2016-12-01 10:26:02 UTC  
> Updated at: 2016-12-01 11:50:53 UTC  
> Closed at: 2016-12-01 11:50:53 UTC  
> Url: https://github.com/boostorg/compute/issues/671  

In the queue there are pairs of functions for all enqueue operations, one synchronous, one asynchronous.  
  
e.g. we have  
```  
enqueue_map_buffer//Synchronous  
enqueue_map_buffer_async//Asynchronous  
```  
  
but  
`enqueue_unmap_buffer //Asynchronous`  
  
<bountysource-plugin>  
---  
Want to back this issue? **[Post a bounty on it!](https://www.bountysource.com/issues/39678171-rename-compute_queue-enqueue_unmap_buffer-to-enqueue_unmap_buffer_async?utm_campaign=plugin&utm_content=tracker%2F402515&utm_medium=issues&utm_source=github)** We accept bounties via [Bountysource](https://www.bountysource.com/?utm_campaign=plugin&utm_content=tracker%2F402515&utm_medium=issues&utm_source=github).  
</bountysource-plugin>

---

## Comment 1

> Username: Ulfgard  
> Created at: 2016-12-01 11:50:53 UTC  
> Url: https://github.com/boostorg/compute/issues/671#issuecomment-264152638  

i see that there is a naming convention that all non-copy operations are not marked as async.
