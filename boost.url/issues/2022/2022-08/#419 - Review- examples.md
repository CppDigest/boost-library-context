# #419 - Review: examples [Closed]

> Username: alandefreitas  
> Created at: 2022-08-16 23:57:52 UTC  
> Updated at: 2022-10-17 21:30:44 UTC  
> Closed at: 2022-10-17 21:30:44 UTC  
> Url: https://github.com/boostorg/url/issues/419  

From the review  
  
> In particular, I miss an example (and possibly a page) about composing  
and mutating URLs (starting with a base URL, add some segments and query  
parameters, emphasizing on when percent encoding gets applied).  
This is a pretty typical task when you're consuming a third party  
REST API.  
  
> I'd add a page and an example on percent encoding functions, as they  
are only explained in the reference and the explanation assumes  
familiarity with the CharSet concept.  
  
Comments  
  
> I would find this useful (this builds a QR code using Google's  
Infographic API):  
  
```cpp  
#include <boost/url.hpp>  
#include <iostream>  
  
using namespace boost::urls;  
  
int  
main(int argc, char **argv)  
{  
url u ("https://chart.googleapis.com/");  
u.segments().insert(u.segments().end(), "charts");  
u.params().append("cht", "qr");  
u.params().append("chs", "600x600");  
u.params().append("chs", argv[1]);  
  
std::cout << u << std::endl;  
}  
```  
  
> small examples may be  
even more useful to newcomers.  
Having this would have saved me some  
time when coding my use case.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-17 17:37:23 UTC  
> Url: https://github.com/boostorg/url/issues/419#issuecomment-1218311622  

I would like to see if we can find many examples of specs for building URIs for APIs - like this one for Google's QR code thingy. We could combine them into one big example program with a command-line driver or we could just make each of them their own tiny program like the one above.

---

## Comment 2

> Username: sehe  
> Created at: 2022-08-18 06:37:48 UTC  
> Url: https://github.com/boostorg/url/issues/419#issuecomment-1219093506  

+1 for short examples, command line drivers with option parsing muddy the code (depending a bit on what they're used for). We can assume people know how to do that.  
  
I think I'd like to see `resolve` used in that example, unless I misunderstood it's applicability and it should not be used here.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-08-22 06:39:14 UTC  
> Url: https://github.com/boostorg/url/issues/419#issuecomment-1221914974  

More comments  
  
> I found `resolve` a bit mystifying. While I appreciate the docs not trying to  
> duplicate the RFC specifying what the operation means, I think it will be a big  
> help to have inspirational examples, like example one that I came up with a  
> while ago:  
>   
>     boost::url make_url(boost::url_view base_api, boost::url_view method) {  
>         assert(!method.is_path_absolute());  
>         assert(base_api.data()[base_api.size() - 1] == '/');  
>   
>         boost::urls::error_code ec;  
>         boost::url              url;  
>         resolve(base_api, method, url, ec);  
>         if (ec)  
>             throw boost::system::system_error(ec);  
>         return url;  
>     }  
>   
>     // example of use:  
>     static boost::url_view const base_api{"wss://[api.binance.com/api/v3/](http://api.binance.com/api/v3/)"};  
>   
>     boost::url method{"depth"};  
>     method.params().append("symbol", "BTCUSDT");  
>   
>     std::cout << make_url(base_api, method); // "wss://[api.binance.com/api/v3/depth?symbol=BTCUSDT](http://api.binance.com/api/v3/depth?symbol=BTCUSDT)"

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-08-22 06:41:11 UTC  
> Url: https://github.com/boostorg/url/issues/419#issuecomment-1221916761  

> Perhaps the examples would be better when  
> fleshed out in long form, then linked to.  
>

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-08-22 20:56:49 UTC  
> Url: https://github.com/boostorg/url/issues/419#issuecomment-1223021661  

> I would recommend not to start your example page with a 700 lines  
> implementation of magnet links, that's super intimidating.  
>

---

## Comment 6

> Username: alandefreitas  
> Created at: 2022-08-22 20:57:02 UTC  
> Url: https://github.com/boostorg/url/issues/419#issuecomment-1223022585  

> Something I missed was a simple example showing that the parse functions  
> also accept unknown schemes like opc.tcp:// for example.  
>

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-10-03 22:19:29 UTC  
> Url: https://github.com/boostorg/url/issues/419#issuecomment-1266127733  

we still need more small examples...

---

## Comment 8

> Username: alandefreitas  
> Created at: 2022-10-07 22:37:34 UTC  
> Url: https://github.com/boostorg/url/issues/419#issuecomment-1272137754  

I've been thinking of more examples.  
  
Besides #165, I can't think of anything else. The binance example seems a little boring and redundant now that we have the qrcode example.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2022-10-08 00:09:31 UTC  
> Url: https://github.com/boostorg/url/issues/419#issuecomment-1272171288  

Ask the mailing list
