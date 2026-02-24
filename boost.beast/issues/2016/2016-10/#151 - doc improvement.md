# #151 - doc improvement [Closed]

> Username: seeekr  
> Created at: 2016-10-21 01:07:24 UTC  
> Updated at: 2016-10-31 21:00:22 UTC  
> Closed at: 2016-10-31 21:00:22 UTC  
> Url: https://github.com/boostorg/beast/issues/151  

I'll likely use this issue to write down a few observations & suggestions for improvement (& tasks, possibly for future PRs for myself) about the current docs. For now just one issue:  
  
1) **(discussion below -- mostly an issue with experience in boost::asio development)**  
  
Magic incantation to get a `DynamicBuffer` to cooperate and make itself available as a `string` (or similar, just anything useful that can be written not just to streams and sockets):  
  
**(below code is wrong and should not be used; instead `beast::to_string(buf.data())` is there for this purpose)**  
  
```  
beast::streambuf buf; // gotten from calling read operation  
std::string s(boost::asio::buffers_begin(buf.data()), boost::asio::buffers_begin(buf.data()) + buf.size());  
```  
  
(Totally lifted from a [SO answer](http://stackoverflow.com/a/13781198/385004).)  
  
It might just be my inexperience in working with boost and Beast, but the only info I found about converting this buffer was some not very simple code here (which is also doing fancy optimization iiuc, and with the purpose being more than just simple conversion): https://github.com/vinniefalco/Beast/blob/1e3543f63ed46bb1b2e698939f3cf0b1324dc180/test/websocket/websocket_sync_echo_server.hpp#L158  
  
```  
template<class DynamicBuffer, std::size_t N>  
    static  
    bool  
    match(DynamicBuffer& db, char const(&s)[N]) { ... }  
```  
  
For reference also the docs for `boost::asio::streambuf`: http://www.boost.org/doc/libs/1_61_0/doc/html/boost_asio/reference/basic_streambuf.html#boost_asio.reference.basic_streambuf.examples  
The example using `std::istream` for converting to a `string` did not work for me. Was it just me or is there simply not 100% compatibility between the beast and boost types buffer types?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-10-21 01:10:49 UTC  
> Url: https://github.com/boostorg/beast/issues/151#issuecomment-255268986  

Hmm.. no, that code is not correct. Remember that a **ConstBufferSequence** is a _list_ of buffers. Your code only accesses the first element. You need to loop over all the buffers and append them to the string. Fortunately, Beast already provides such a function, its called `beast::to_string`:  
http://vinniefalco.github.io/beast/beast/ref/to_string.html  
  
Source code:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/core/to_string.hpp#L17

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-10-21 01:12:57 UTC  
> Url: https://github.com/boostorg/beast/issues/151#issuecomment-255269261  

This code returns `true` if the front of the buffer matches the passed string:  
https://github.com/vinniefalco/Beast/blob/1e3543f63ed46bb1b2e698939f3cf0b1324dc180/test/websocket/websocket_sync_echo_server.hpp#L158  
  
Boost.Asio provides adapters to let you use buffer sequences with `regex`, see:  
http://www.boost.org/doc/libs/1_62_0/doc/html/boost_asio/reference/buffers_iterator.html

---

## Comment 3

> Username: seeekr  
> Created at: 2016-10-21 01:15:38 UTC  
> Url: https://github.com/boostorg/beast/issues/151#issuecomment-255269591  

Ah damn, you're right. That way of doing it is of course going to blow up for anything that has more than a single buffer. I was too happy to have found any solution to have closely scrutinized what exactly the code might be doing. Thanks!

---

## Comment 4

> Username: vinniefalco  
> Created at: 2016-10-21 01:15:45 UTC  
> Updated at: 2016-10-21 01:17:04 UTC  
> Url: https://github.com/boostorg/beast/issues/151#issuecomment-255269611  

> The example using std::istream for converting to a string did not work for me.  
  
`boost::asio::streambuf` derives from `std::basic_streambuf`, inheriting all of its limitations  (its a known problem that the iostreams in the standard library are deficient). Beast stream buffers do not derive from any standard library types, so Asio sample code that depends on this will not work. However, compatibility with standard iostreams is not part of the **DynamicBuffer** requirements, see:  
http://vinniefalco.github.io/beast/beast/ref/DynamicBuffer.html  
  
This definition is identical to the one in the Networking-TS:  
http://cplusplus.github.io/networking-ts/draft.pdf

---

## Comment 5

> Username: seeekr  
> Created at: 2016-10-21 01:18:33 UTC  
> Url: https://github.com/boostorg/beast/issues/151#issuecomment-255269975  

Ah, I see, thanks for clarifying. Might be an issue only lesser experienced C++ devs could run into.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2016-10-21 01:19:42 UTC  
> Updated at: 2016-10-21 01:20:04 UTC  
> Url: https://github.com/boostorg/beast/issues/151#issuecomment-255270101  

Asio is not for beginners, and since Beast builds on top of Asio, it inherits some of the learning barriers. I'm open to suggestions on how to improve this but lets face it, asynchronous operations and template concepts have a minimum proficiency requirement.  
  
Example code is probably the most useful

---

## Comment 7

> Username: seeekr  
> Created at: 2016-10-21 01:27:41 UTC  
> Url: https://github.com/boostorg/beast/issues/151#issuecomment-255271105  

Yeah, for sure, this is not a place where a lot of newbie developers are going to hang out. In this case one thing one might consider adding to the docs is a clear section/example on how to work with the `DynamicBuffer` concept beyond just an echo example -- i.e. something like "parse json, print value from that" or "parse with regex, print that".  
Even just the link to [boost::asio::buffers_iterator](http://www.boost.org/doc/libs/1_62_0/doc/html/boost_asio/reference/buffers_iterator.html) is helping a lot. I understand that this is in a sense not really something Beast should need to teach its users, but I also think that there is a lot of potential for somewhat experienced devs from other languages and environments (Node.js, Java) coming to C++ because of how well C++ is currently modernizing, and the community that is building/expanding... and those folks (like me) could benefit a lot from just the right pointers in the right places :)  
In the end you know best what to include in docs and what not, just wanted to share my perspective!

---

## Comment 8

> Username: seeekr  
> Created at: 2016-10-21 02:08:21 UTC  
> Url: https://github.com/boostorg/beast/issues/151#issuecomment-255276146  

But this is really weird: Even the boost::asio docs display code like the above (non-)"solution" that I had used: [boost_asio/overview/buffers](http://www.boost.org/doc/libs/1_60_0/doc/html/boost_asio/overview/core/buffers.html#boost_asio.overview.core.buffers.bytewise_traversal_of_buffer_sequences)  
So... what does this mean? I could imagine that the iterators would just walk over all the bytes of the (potentially) multiple (potentially non-contiguous) buffers properly, hiding the complexity behind the iterator abstraction, no?  
(With `beast::to_string()` available this is now mostly an issue for understanding better what's actually going on. Though `to_string()` could be simplified a bit using this code, if it does work indeed.)

---

## Comment 9

> Username: vinniefalco  
> Created at: 2016-10-21 11:07:28 UTC  
> Url: https://github.com/boostorg/beast/issues/151#issuecomment-255354568  

You correct that the boost iterator adaptor transforms the separate buffers into what looks like a single range whose iterators meet the requirements of **BidirectionalIterator** (http://en.cppreference.com/w/cpp/concept/BidirectionalIterator)  
  
You could simplify `to_string` a tiny bit using that adapter but the result will be suboptimal, since a good implementation of `std::basic_string::append` will use `std::memcpy` when passed a pointer.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2016-10-21 11:08:36 UTC  
> Url: https://github.com/boostorg/beast/issues/151#issuecomment-255354763  

Just a reminder, the docs aren't done yet (are they ever?). Notably missing is an entire section discussing the "Core" classes, which includes the various buffer adapters and buffer utilities. See **Core** here:  
http://vinniefalco.github.io/beast/beast/ref.html

---

## Comment 11

> Username: vinniefalco  
> Created at: 2016-10-31 15:47:35 UTC  
> Url: https://github.com/boostorg/beast/issues/151#issuecomment-257331045  

Does this issue require action? Or can it be closed?

---

## Comment 12

> Username: seeekr  
> Created at: 2016-10-31 21:00:22 UTC  
> Url: https://github.com/boostorg/beast/issues/151#issuecomment-257419166  

I think not directly, no. Didn't run into anything else that felt like a significant (potential) issue with docs etc while implementing a service on top of Beast, so I think we can close this! Thanks!
