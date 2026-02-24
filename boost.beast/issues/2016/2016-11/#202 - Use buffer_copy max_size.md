# #202 - Use buffer_copy max_size [Closed]

> Username: vinniefalco  
> Created at: 2016-11-27 14:26:47 UTC  
> Updated at: 2017-06-08 05:29:42 UTC  
> Closed at: 2017-06-08 05:29:42 UTC  
> Url: https://github.com/boostorg/beast/issues/202  

Several pieces of code can be simplified to pass `max_size` in calls to `buffer_copy` instead of using `prepare_buffer`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-08 05:29:42 UTC  
> Url: https://github.com/boostorg/beast/issues/202#issuecomment-307002579  

All done. `prepare_buffer` is now `buffer_prefix`.
