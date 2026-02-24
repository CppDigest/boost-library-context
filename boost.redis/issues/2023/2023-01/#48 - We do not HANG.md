# #48 - We do not HANG [Closed]

> Username: vinniefalco  
> Created at: 2023-01-19 16:11:33 UTC  
> Updated at: 2023-02-19 09:35:56 UTC  
> Closed at: 2023-02-19 09:35:56 UTC  
> Url: https://github.com/boostorg/redis/issues/48  

`async_receive` Javadoc should not use the word "hang". Consider this instead:  
  
> When pushes arrive and there is no `async_receive` operation in progress, pushed data, requests, and responses will be paused until async_receive is called again

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-02-18 20:41:09 UTC  
> Url: https://github.com/boostorg/redis/issues/48#issuecomment-1435764907  

Fixed: https://github.com/boostorg/redis/commit/8b02268182fdf77d8fb16a5a3887b5926f37df06#diff-9de8f3a7aafe6aa5ccdd13d12ef3dca7626a5e8006b1d7cd16e6efa017765bb7R142
