# #1644 - http::chunk_last::chunk_last does not support chunk extensions [Closed]

> Username: ivilata  
> Created at: 2019-06-25 21:21:22 UTC  
> Updated at: 2020-01-13 18:15:55 UTC  
> Closed at: 2020-01-02 23:05:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1644  

According to current development [http::chunk_last::chunk_last's documentation](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__chunk_last/chunk_last.html), there doesn't seem to be a way of providing chunk extensions for the final chunk (other than crafting the last chunk manually).  
  
[RFC 7230 section 4.1](https://tools.ietf.org/html/rfc7230#section-4.1) (*Chunked Transfer Encoding*) states that the last chunk can also have such extensions:  
  
```  
last-chunk     = 1*("0") [ chunk-ext ] CRLF  
```  
  
I guess that most of the current code in `chunk_body` may be reused to implement this.  
  
Thanks!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-06-25 22:24:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1644#issuecomment-505645525  

Hmm it seems you're right!

---

## Comment 2

> Username: ivilata  
> Created at: 2019-09-10 10:28:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1644#issuecomment-529874470  

I readied an alternate implementation of `chunk_last` and `make_chunk_last` ([chunk_last_x](https://github.com/equalitie/ouinet/blob/http-fwd-chunk-ext/src/util/chunk_last_x.h)). It's based off Boost 1.71, however:  
  
 1. It uses `boost::asio::const_buffer` et al. instead of `net::const_buffer` since we use an older version of Boost in our project.  
 2. Declarations and definitions are crammed into the `.h` file (in different sections, though).  
 3. Not all variations available from constructors are accessible via `make_chunk_last_x` functions (I had issues with variable length template arguments matching too much).  
  
If the thing seems ok in general I can prepare a PR as proper modifications to Beast's files. Any indications on missing stuff are very welcome, since I'm not very experienced with preparing PRs for Beast. :stuck_out_tongue: Thanks!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-09-10 12:41:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1644#issuecomment-529916274  

I'm glad to see that you found a workaround, this looks like a good solution for you. I'm not entirely happy with the current chunked encoding interfaces, but I need time to think about how they might be improved to be more friendly.

---

## Comment 4

> Username: ivilata  
> Created at: 2020-01-02 21:53:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1644#issuecomment-570366146  

We ended up using a more abstract wrapper over the response parser ([reader](https://github.com/equalitie/ouinet/blob/response-reader/src/response_reader.h) and [part](https://github.com/equalitie/ouinet/blob/response-reader/src/response_part.h) if you feel curious) that handles responses as a series of parts (head, body, chunk header, chunk body and trailer, so chunk headers take care of extensions).  
  
As far as I am concerned, feel free to close the issue. :smile: Thanks!

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-01-02 23:05:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1644#issuecomment-570390524  

Beautiful, this is exactly what I had in mind that users would be able to do with Beast, which is to write their own higher level abstractions using the building blocks that I provided!!

---

## Comment 6

> Username: ivilata  
> Created at: 2020-01-13 18:15:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1644#issuecomment-573797761  

Kudos to @inetic who designed and wrote those classes! :smile:
