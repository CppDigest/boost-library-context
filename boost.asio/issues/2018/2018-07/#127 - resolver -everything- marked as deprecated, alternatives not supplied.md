# #127 - resolver "everything" marked as deprecated, alternatives not supplied [Closed]

> Username: xxxLCxxx  
> Created at: 2018-07-02 08:42:45 UTC  
> Updated at: 2020-12-30 00:52:07 UTC  
> Closed at: 2020-12-30 00:52:06 UTC  
> Url: https://github.com/boostorg/asio/issues/127  

If I'm correct, Boost 1.67.0 is the latest. As you can see here  
https://www.boost.org/doc/libs/1_67_0/doc/html/boost_asio/reference/ip__tcp/resolver.html  
(Add link in this editor is not working, hurray. Preview neither - extra bonus!)  
about everything you need to resolve is marked as deprecated:  
  
async_resolve (Deprecated.) ...  
resolve (Deprecated.) ...  
query (Deprecated.) The query type.  
iterator (Deprecated.) The iterator type.  
  
This would render many of the examples deprecated as well, as the clients are using those functions for resolving.  
What are we supposed to use for asynchronous lookups now?

---

## Comment 1

> Username: djarek  
> Created at: 2018-07-02 21:23:44 UTC  
> Url: https://github.com/boostorg/asio/issues/127#issuecomment-401940806  

Please check the documentation closely, it's clearly stated that only some overloads are deprecated.

---

## Comment 2

> Username: xxxLCxxx  
> Created at: 2018-07-03 09:08:43 UTC  
> Url: https://github.com/boostorg/asio/issues/127#issuecomment-402067696  

Shouldn't that be where the 'deprecated' is located?  
=> this overload(!) is deprecated, use ... instead?  
If you look for the resolver documentation and land at ip::tcp::resolver, this is far from clear.  
Besides, it points to ip::basic_resolver, which has just the same deprecated entries without further information. This isn't the way to document things.

---

## Comment 3

> Username: xxxLCxxx  
> Created at: 2018-07-04 14:24:07 UTC  
> Url: https://github.com/boostorg/asio/issues/127#issuecomment-402493941  

Am I blind?  
https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/quick_start.html  
...  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
...  
// Look up the domain name  
auto const results = resolver.resolve(host, port);  
...  
  
Is that the 'deprecated' one - in your (Beast) current "Getting started" guide?  
Which would be the correct (non deprecated) way?  
*puzzled*

---

## Comment 4

> Username: xxxLCxxx  
> Created at: 2018-07-04 14:44:58 UTC  
> Url: https://github.com/boostorg/asio/issues/127#issuecomment-402499412  

forgot this in the middle:  
tcp::resolver resolver{ioc};

---

## Comment 5

> Username: djarek  
> Created at: 2018-07-04 20:10:13 UTC  
> Updated at: 2018-07-04 20:10:47 UTC  
> Url: https://github.com/boostorg/asio/issues/127#issuecomment-402552409  

The Beast guide uses this function: https://www.boost.org/doc/libs/1_67_0/doc/html/boost_asio/reference/ip__basic_resolver/resolve/overload3.html

---

## Comment 6

> Username: xxxLCxxx  
> Created at: 2018-07-05 05:45:58 UTC  
> Url: https://github.com/boostorg/asio/issues/127#issuecomment-402612429  

I finally understand what is meant. In the reference, when there are 'deprecated' member functions, this is being propagated upwards where then the entire/only member function description has a "(Deprecated.)" appended.  
This seems to be automated. I would suggest turning that into something like "(1 of 7 deprecated)". This would be of more use to the 'creators' and a lot less misleading to anyone stumbling on the reference.

---

## Comment 7

> Username: arthur-tacca  
> Created at: 2018-07-17 17:21:15 UTC  
> Url: https://github.com/boostorg/asio/issues/127#issuecomment-405660115  

> it's ... stated that only some overloads are deprecated.  
  
Correct  
  
> it's clearly stated that only some overloads are deprecated.   
  
Not correct. (Clearly not correct 😉 )  
  
I have had the same confusion before. The ideal solution would be to tweak the documentation generator so the distinction between overloads is clearer, but you *do* want overloads grouped together somewhat so imagine it would be very hard to get the balance right. The simpler solution is the one xxxLXxxx suggested: just change the text from "(deprecated)" to "(this overload is deprecated)" for the relevant functions.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-10-11 15:11:59 UTC  
> Url: https://github.com/boostorg/asio/issues/127#issuecomment-428993349  

>The ideal solution would be to tweak the documentation generator...  
  
Pull requests welcomed, the code to generate those docs is here:  
https://github.com/boostorg/asio/blob/fbe86d86b1ac53e40444e5af03ca4a6c74c33bda/doc/reference.xsl#L1281

---

## Comment 9

> Username: srijanc  
> Created at: 2020-07-03 08:08:12 UTC  
> Url: https://github.com/boostorg/asio/issues/127#issuecomment-653414170  

I am still unable to figure out what exactly needs to be used in place of the "iterator" or any pre-existing is being used instead. Please provide some suggestions.

---

## Comment 10

> Username: ghost  
> Created at: 2020-12-30 00:52:05 UTC  
> Url: https://github.com/boostorg/asio/issues/127#issuecomment-752289677  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#626](https://github.com/chriskohlhoff/asio/issues/626).
