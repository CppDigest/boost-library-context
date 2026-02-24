# #19 - Consumers / producers example [Closed]

> Username: Eddie-cz  
> Created at: 2022-07-27 08:48:07 UTC  
> Updated at: 2022-08-07 11:28:27 UTC  
> Closed at: 2022-08-07 11:28:27 UTC  
> Url: https://github.com/boostorg/redis/issues/19  

Hello,  
 I think it would be nice, following our conversation, to have some 'real life' example with multiple consumers and producers setting getting data, and also to show how to use various completition tokens in async execs, how to properly ensure requests lifetime, their reusal and so on.  If possible, in C++17 standard. Petr

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-07-27 20:24:20 UTC  
> Url: https://github.com/boostorg/redis/issues/19#issuecomment-1197328420  

Ok, I will work on that on the weekend. I am planning to improve support for synchronous calls along the lines of this example https://github.com/mzimbres/aedis/blob/317a185eb0398ebd14241aeb11ea7c63664a44e1/examples/intro_sync.cpp  
which is still experimental. It is a wrapper over a connection whose member functions can be called from any thread in a synchronous way and hides Asio code completely away from the user.

---

## Comment 2

> Username: Eddie-cz  
> Created at: 2022-07-30 11:16:37 UTC  
> Url: https://github.com/boostorg/redis/issues/19#issuecomment-1200139618  

I like that, it will be useful for a lot of library users 👍  
  
Dne st 27. 7. 2022 22:24 uživatel Marcelo ***@***.***> napsal:  
  
> Ok, I will work on that on the weekend. I am planning to improve support  
> for synchronous calls along the lines of this example  
> https://github.com/mzimbres/aedis/blob/317a185eb0398ebd14241aeb11ea7c63664a44e1/examples/intro_sync.cpp  
> which is still experimental. It is a wrapper over a connection whose  
> member functions can be called from any thread in a synchronous way and  
> hides Asio code completely away from the user.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/mzimbres/aedis/issues/19#issuecomment-1197328420>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ALR2PQ7PSF75MA5HPYUJCILVWGLH5ANCNFSM54YX4WCQ>  
> .  
> You are receiving this because you authored the thread.Message ID:  
> ***@***.***>  
>

---

## Comment 3

> Username: mzimbres  
> Created at: 2022-08-07 10:35:44 UTC  
> Url: https://github.com/boostorg/redis/issues/19#issuecomment-1207378226  

> Hello, I wanted to incorporate aedis into our production version.  
  
That is great. I am about to add  a "Who is using Aedis" page where you can list your company or project.  
  
> While using examples, it was all ok.  Now, I kinda struggle whenusing aedis in async environment.  On startup: I run ioc.run in custom thread in constructor, then I async_run connection and async_exec auth and hello.  In tick loop of our program, I tried to set some keys to redis.  Only way how I can do it is using use_future completion token and wait for result.  I suggest to make some example where ioc runs in custom thread and other threads runs exec of requests. And don't wait for result.  
  
I have just released Aedis v0.3.0 that has experimental support for synchronous calls. The design changed a little in comparison to what I posted before, but not much, see [intro_sync.cpp](https://github.com/mzimbres/aedis/blob/master/examples/intro_sync.cpp). For how to upgrade to the new release see the[changelog](https://mzimbres.github.io/aedis/md_CHANGELOG.html).  
  
Regarding  
  
> And don't wait for result.  
  
This is not possible in synchronous code like above, but given Redis is very fast, I think you should not care about this. Otherwise, just use `async_exec`. To guarantee that the lifetime of the request object you can keep a copy of the request in the completion handler, for example  
  
```  
auto req = std::make_shared<request>();  
// Push some commands.  
conn->async_exec(*req, aedis::adapt(), [req](auto...){});  
```  
  
If the call must occur across threads use dispatch.  
  
```  
auto f = [conn]()  
{  
   auto req = std::make_shared<request>();  
   // Push some commands.  
  
   conn->async_exec(*req, aedis::adapt(), [req](auto...){});  
}  
  
boost::asio::dispatch(boost::asio::bind_executor(conn.get_executor(), f));  
```  
  
and you should be fine.

---

## Comment 4

> Username: mzimbres  
> Created at: 2022-08-07 10:45:07 UTC  
> Url: https://github.com/boostorg/redis/issues/19#issuecomment-1207379714  

Hello,  
  
> I think it would be nice, following our conversation, to have some 'real life' example with multiple consumers and producers setting getting data,  
  
Can you give more detail about what kind of example would fill your expectations here? I have been thinking about adding an a sync version of the echo_server.cpp, would that cover what you want?  
  
> and also to show how to use various completition tokens in async execs, how to properly ensure requests lifetime, their reusal and so on. If possible, in C++17 standard. Petr  
  
Most of this is more about teaching ASIO than Aedis, I will try to find time to write a tutorial, at the moment however I am focused in improving CI, test coverage and documentation. I plan to propose Aedis to boost in the near future.

---

## Comment 5

> Username: Eddie-cz  
> Created at: 2022-08-07 11:28:27 UTC  
> Url: https://github.com/boostorg/redis/issues/19#issuecomment-1207387051  

> Hello,  
>   
> > I think it would be nice, following our conversation, to have some 'real life' example with multiple consumers and producers setting getting data,  
>   
> Can you give more detail about what kind of example would fill your expectations here? I have been thinking about adding an a sync version of the echo_server.cpp, would that cover what you want?  
  
I think sync version covered this request. Btw I tried 0.3.0, so far so good.  
  
>   
> > and also to show how to use various completition tokens in async execs, how to properly ensure requests lifetime, their reusal and so on. If possible, in C++17 standard. Petr  
>   
> Most of this is more about teaching ASIO than Aedis, I will try to find time to write a tutorial, at the moment however I am focused in improving CI, test coverage and documentation. I plan to propose Aedis to boost in the near future.  
  
You are right, it's really more about ASIO than Aedis. Improvements in areas you mentioned are definitely more important.
