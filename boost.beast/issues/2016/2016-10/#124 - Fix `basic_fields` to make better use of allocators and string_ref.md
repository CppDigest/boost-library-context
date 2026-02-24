# #124 - Fix `basic_fields` to make better use of allocators and string_ref [Closed]

> Username: ahmedcharles  
> Created at: 2016-10-10 16:14:35 UTC  
> Updated at: 2017-05-10 02:21:46 UTC  
> Closed at: 2017-05-10 02:21:46 UTC  
> Url: https://github.com/boostorg/beast/issues/124  



---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-10-10 16:16:53 UTC  
> Url: https://github.com/boostorg/beast/issues/124#issuecomment-252668818  

No argument on this one ^_^

---

## Comment 2

> Username: ahmedcharles  
> Created at: 2016-10-10 16:26:03 UTC  
> Url: https://github.com/boostorg/beast/issues/124#issuecomment-252671050  

I don't mind making an attempt to fix this, though I'm not as familiar with the intended design as you might be.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-10-10 16:37:01 UTC  
> Url: https://github.com/boostorg/beast/issues/124#issuecomment-252673747  

It would probably be more efficient to propose declarations here instead of going through the trouble of a branch. You bring up an important question - what is the intended design? I don't think the design of `basic_headers` is particularly strong.  
  
Headers interact with algorithms in two ways. They get serialized (`write`), and they get parsed (by `parser_v1`). For writing, I created a concept called **FieldSequence**. Any **Headers** type that meets these requirements can be serialized:  
http://vinniefalco.github.io/beast/beast/ref/FieldSequence.html  
  
For `parser_v1` the requirements are not well defined. But you can infer them, there needs to be a function called `insert` that takes the field name and the field value. I hesitate to write out the requirements because I am unsure what the needs are.  
  
One question that comes up, will `basic_headers` address everyone's use-case? What are the use-cases that we want to address? The library needs to come with a default headers implementation that works for most people (and I think `basic_headers` provides that, or at least comes close). And I think that the concepts should allow people to implement their own headers type of object for specialized purposes.  
  
Here's an example. Recognize that a server, likely understands a fixed number of field names. And those names are known at compile time. A sophisticated headers implementation could only insert the headers that the server understands. And it could use a perfect hash function generated statically to put those values into an unordered map with no collisions. The field name would be stored as a simple integer, and when it has to be converted to a string (say, for serialization), the iterator it provides could perform that lookup and return the full string.  
  
Is this an important use-case? Maybe. Someone advanced the idea in the review of Boost.Http (a competing library).  
  
Before changing `basic_headers` around, it would be great to get more feedback from stakeholders and interested parties on what the requirements need to be. Otherwise, there's a good chance of wasted efforts.

---

## Comment 4

> Username: ahmedcharles  
> Created at: 2016-10-10 16:43:21 UTC  
> Updated at: 2016-10-10 16:52:35 UTC  
> Url: https://github.com/boostorg/beast/issues/124#issuecomment-252675243  

I've also seen headers implemented by providing a fixed size buffer, which is then used by the parser to write the header values into. So, what is effectively arena allocation is another option and obviously, it can be implemented in addition to the approach you mention.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2016-10-10 17:08:35 UTC  
> Url: https://github.com/boostorg/beast/issues/124#issuecomment-252681204  

> arena allocation is another option  
  
Yes, and that was my original intention behind providing the Allocator template parameter. But it has problems, as you can see.

---

## Comment 6

> Username: viccpp  
> Created at: 2016-10-10 17:36:26 UTC  
> Updated at: 2016-10-10 17:47:07 UTC  
> Url: https://github.com/boostorg/beast/issues/124#issuecomment-252688053  

> One question that comes up, will basic_headers address everyone's use-case?  
  
Сertainly! Generic library for HTTP must provide a way to use custom allocators. And providing both - convenient specialization `http::headers` for 90%+ of users and customizable `http::basic_headers` for the rest is a good design choice, as for me.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2016-10-11 13:03:02 UTC  
> Url: https://github.com/boostorg/beast/issues/124#issuecomment-252910169  

I'm thinking about changing `message` so that string fields such as `method`, `uri`, and `reason` are stored in the **Headers** object. They would be stored using the special field names `":method"`, `":path"`, and `":reason"` respectively. This is not a new idea, HTTP/2 prescribes this in its serialization format.  
  
The benefit of doing this, is that the **Headers** type can control the memory allocations. We can add accessors to the `message` object, such as:  
  
```  
struct message  
{  
  // get the URI  
  boost::string_view uri() const;  
  
  // set the URI  
  void uri(std::string);  
  void uri(boost::string_view const&);  
  
  ...  
};  
```  
  
This would be a breaking change. Fixing it is fairly trivial though.

---

## Comment 8

> Username: ahmedcharles  
> Created at: 2016-10-11 13:20:40 UTC  
> Url: https://github.com/boostorg/beast/issues/124#issuecomment-252914299  

I'm working on a prototype headers implementation currently, which I'm loosely basing on the HPACK RFC. It's not intended to implement the RFC, but just take ideas from it. Anyways, I'm probably 1/3rd done, so I should have something that I can submit as a PR for feedback in a day or two, probably.  
  
Interestingly, it also supports storing things in the the pseudo header fields, though reason isn't one of them: "HTTP/2 does not define a way to carry the version or reason phrase that is included in an HTTP/1.1 status line." They are:  
- `:method`  
- `:scheme`  
- `:authority`  
- `:path`  
- `:status`

---

## Comment 9

> Username: vinniefalco  
> Created at: 2016-10-11 13:25:23 UTC  
> Url: https://github.com/boostorg/beast/issues/124#issuecomment-252915440  

The Reason-Phrase is fairly obsolete

---

## Comment 10

> Username: vinniefalco  
> Created at: 2016-11-12 21:59:53 UTC  
> Url: https://github.com/boostorg/beast/issues/124#issuecomment-260151216  

"version" is not a problem, we just tuck that into the `header` or `message` object as an `int` - it does no harm, and requires no dynamic allocation.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-05-03 00:28:47 UTC  
> Url: https://github.com/boostorg/beast/issues/124#issuecomment-298797682  

This is happening!
