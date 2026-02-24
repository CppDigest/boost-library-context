# #56 - channel example not working [Closed]

> Username: matthijs  
> Created at: 2023-07-26 19:44:40 UTC  
> Updated at: 2023-07-27 04:28:48 UTC  
> Closed at: 2023-07-27 04:28:48 UTC  
> Url: https://github.com/boostorg/cobalt/issues/56  

Hi,  
  
I am playing around with this library and it seems useful for me. 👍   
  
The example given in the channel.adoc didn't work, after adjusting it to:  
```c++  
async::promise<void> producer(channel<int> & chan)  
{  
  for (int i = 0; i < 4; i++)  
    co_await chan.write(i);  
  chan.close();  
}  
  
async::main co_main(int argc, char * argv[])  
{  
  async::channel<int> c{1U, co_await async::this_coro::executor}; // added 1U as first parameter.  
  auto w = producer(chan); // added  
  
  while (c.is_open())  
    std::cout << co_await c.read() << std::endl;  
  
  co_return 0;  
}  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-07-27 03:19:42 UTC  
> Url: https://github.com/boostorg/cobalt/issues/56#issuecomment-1652851991  

Thanks, I'll fix that in the docs! Note that a channel size of 0 makes it  a synchronization point.
