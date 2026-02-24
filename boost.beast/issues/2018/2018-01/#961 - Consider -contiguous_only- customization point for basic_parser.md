# #961 - Consider "contiguous_only" customization point for basic_parser [Closed]

> Username: djarek  
> Created at: 2018-01-02 01:11:31 UTC  
> Updated at: 2019-01-02 02:42:39 UTC  
> Closed at: 2019-01-02 02:42:39 UTC  
> Url: https://github.com/boostorg/beast/issues/961  

There are use-cases where a user exclusively uses DynamicBuffers which produce contiguous buffer sequences. Consider allowing a user to select if they want to allow only contiguous buffer sequences, so that they can achieve lower code size and heap memory usage (by conditionally removing the `buf_` and `buf_len_` members from `basic_parser`).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-02 01:16:56 UTC  
> Url: https://github.com/boostorg/beast/issues/961#issuecomment-354690507  

idk man you're talking about saving 8 or 16 bytes in the parser.. there are no heap allocations if the input is already contiguous. I'm having a hard time getting excited about saving 16 bytes.

---

## Comment 2

> Username: djarek  
> Created at: 2018-01-02 01:42:20 UTC  
> Url: https://github.com/boostorg/beast/issues/961#issuecomment-354691829  

The parser itself has to be heap allocated (at least the one supplied with Beast) because it's not movable. Anyway, this isn't a huge issue, because the savings are marginal.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-01-02 01:50:55 UTC  
> Url: https://github.com/boostorg/beast/issues/961#issuecomment-354692390  

Ah, I see what you mean.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-01-02 02:14:52 UTC  
> Url: https://github.com/boostorg/beast/issues/961#issuecomment-354693939  

One way to do this is make the parser *only* accept a contiguous buffer, then put the buffering logic into the stream algorithms using a utility class. A benefit of this approach is the parser could become independent of Asio headers, which some users want.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-01-02 02:42:39 UTC  
> Url: https://github.com/boostorg/beast/issues/961#issuecomment-450778518  

Parser is going to be refactored and won't allow a non-contiguous buffer.
