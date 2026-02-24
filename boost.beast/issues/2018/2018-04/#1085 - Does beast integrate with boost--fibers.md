# #1085 - Does beast integrate with boost::fibers? [Closed]

> Username: bitbugprime  
> Created at: 2018-04-01 19:46:07 UTC  
> Updated at: 2018-05-02 20:05:58 UTC  
> Closed at: 2018-05-02 20:05:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1085  

I haven't seen any examples using Beast with fibers. I've used Beast with coroutines, and the fibers API is pretty similar. A different future type though so the use_future tag to make the Beast/asio APIs return futures can't work with fibers and the fibers documentation offers little help on how to write your own tag (or really, help on anything related to asio and fibers). Fibers use a different yield type too (boost::asio::fibers::yield instead of boost::asio::yield_context).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-04-01 20:23:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1085#issuecomment-377814087  

The question would be more accurately phrased "Does Asio integrate with boost::fibers?". If yes, then Beast does as well, and in the same fashion, since Beast copies exactly all of the Asio idioms. Please let us know what you find out!

---

## Comment 2

> Username: cmazakas  
> Created at: 2018-04-02 16:59:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1085#issuecomment-377978210  

Fibers and Asio are tricky. The Fiber docs contain this example: https://www.boost.org/doc/libs/1_66_0/libs/fiber/doc/html/fiber/callbacks/then_there_s____boost_asio__.html

---

## Comment 3

> Username: stale[bot]  
> Created at: 2018-05-02 17:52:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1085#issuecomment-386064382  

This issue has been open for a while with no activity, has it been resolved?
