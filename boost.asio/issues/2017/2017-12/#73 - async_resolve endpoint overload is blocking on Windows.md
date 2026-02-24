# #73 - async_resolve endpoint overload is blocking on Windows [Closed]

> Username: sweatybridge  
> Created at: 2017-12-10 20:50:59 UTC  
> Updated at: 2017-12-11 22:10:04 UTC  
> Closed at: 2017-12-11 22:08:50 UTC  
> Url: https://github.com/boostorg/asio/issues/73  

I ran into this problem while trying to do multiple reverse lookups simultaneously. Each lookup is using a unique instance of `endpoint::resolver`. However, the resolution did not happen asynchronously. A similar problem has been posted on the user list back in 2014: https://lists.boost.org/boost-users/2014/12/83445.php  
  
May I suggest that we update the documentation so that software engineers are aware of such issues in the future?  
 http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/reference/ip__basic_resolver/async_resolve/overload2.html

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-12-11 15:23:24 UTC  
> Updated at: 2017-12-11 15:23:36 UTC  
> Url: https://github.com/boostorg/asio/issues/73#issuecomment-350755843  

But the documentation already states that asynchronous name resolutions on Windows are simulated using a per-io-service thread that the asio implementation provides. Is that insufficient?

---

## Comment 2

> Username: sweatybridge  
> Created at: 2017-12-11 18:47:20 UTC  
> Url: https://github.com/boostorg/asio/issues/73#issuecomment-350819063  

That would indeed be sufficient. But I was not able to find that statement anywhere. Are we possibly looking at different documentation pages?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-12-11 18:56:46 UTC  
> Updated at: 2017-12-11 18:58:07 UTC  
> Url: https://github.com/boostorg/asio/issues/73#issuecomment-350821849  

See **Windows NT, 2000, XP, 2003, Vista, 7 and 8**  
  
http://www.boost.org/doc/libs/1_65_1/doc/html/boost_asio/overview/implementation.html#boost_asio.overview.implementation.windows_nt__2000__xp__2003__vista__7_and_8  
  
* *An additional thread per io_service is used to emulate asynchronous host resolution. This thread is created on the first call to either `ip::tcp::resolver::async_resolve()` or `ip::udp::resolver::async_resolve()`.*  
  
Seems like you have not yet committed the entirety of the Boost.Asio documentation to memory :)

---

## Comment 4

> Username: sweatybridge  
> Created at: 2017-12-11 22:08:50 UTC  
> Url: https://github.com/boostorg/asio/issues/73#issuecomment-350874545  

:) that and also I've not tried enough permutations of search terms.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-12-11 22:10:04 UTC  
> Url: https://github.com/boostorg/asio/issues/73#issuecomment-350874860  

Check out Boost.Beast, HTTP and WebSocket using Boost.Asio:  
https://github.com/boostorg/beast
