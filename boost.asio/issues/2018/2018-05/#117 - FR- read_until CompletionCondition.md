# #117 - FR: read_until CompletionCondition [Closed]

> Username: uecasm  
> Created at: 2018-05-25 01:34:24 UTC  
> Updated at: 2020-12-30 00:51:40 UTC  
> Closed at: 2020-12-30 00:51:39 UTC  
> Url: https://github.com/boostorg/asio/issues/117  

One of the most common problems I see mentioned in mailing lists and other support sites whenever anyone tries to use `read_until` or `async_read_until` for the first time is when they try to combine it with `read`/`async_read` and are surprised when this waits for new bytes instead of returning bytes already contained in the streambuf, the way that `read_until` itself operates.  
  
A step towards making this simpler is if `read_until` had an additional overload that took a `CompletionCondition` (exactly like `read`), which has the following behaviour:  
  
- if the condition is satisfied by (some subset of) the bytes already in the streambuf, it returns immediately with those bytes.  
- if the condition is not satisfied by the current streambuf contents, it performs a read operation to collect the minimum number of additional bytes required to satisfy the condition, then returns those on completion.  As usual, multiple `read_some` operations may be required before the condition is satisfied.  
  
Once this exists, the advice can simply be to always use `read_until` or always use `read` and never mix the two, without losing functionality.  
  
In some respects this is like the existing `MatchCondition` overload, but has the advantage that a `CompletionCondition` can avoid over-reading, and the existing standard completion conditions (typically `transfer_exactly`) can be re-used instead of inventing a new match condition (which have more complex signatures and there don't appear to be any standard ones provided).  
  
An alternate idea that would probably be sufficient for this case is to accept a `size_t` of total bytes to return as a direct parameter rather than wrapped in `transfer_exactly`.  
  
Another possibility is to provide a standard match condition which is the equivalent of `transfer_exactly` -- however I suspect this would be an inferior solution as you would not be able to avoid over-reads that way.  
  
(Related: `MatchCondition` does not appear to be documented at the top level reference of the Boost docs, while `CompletionCondition` is.)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-06-06 12:06:23 UTC  
> Url: https://github.com/boostorg/asio/issues/117#issuecomment-395045339  

> if the condition is not satisfied by the current streambuf contents, it performs a read operation to collect the minimum number of additional bytes required to satisfy the condition,  
  
Please explain how an implementation can accomplish this without reading a single byte at a time.

---

## Comment 2

> Username: uecasm  
> Created at: 2018-06-06 12:27:07 UTC  
> Url: https://github.com/boostorg/asio/issues/117#issuecomment-395050511  

> Please explain how an implementation can accomplish this without reading a single byte at a time.  
  
`CompletionCondition`s in general, and `transfer_exactly`/`transfer_at_least` in particular, return how many bytes they want to read.  You simply read that many bytes less however many bytes you already have.  As I said above.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-06-06 13:35:26 UTC  
> Url: https://github.com/boostorg/asio/issues/117#issuecomment-395070392  

You said `CompletionCondition` is exactly like `MatchCondition`. So for example, a match condition could be `"\r\n\r\n"` appearing in the input. How would you know ahead of time exactly how many bytes you need in order to get that sequence in the input? Or am I not understanding your proposal?

---

## Comment 4

> Username: uecasm  
> Created at: 2018-06-06 21:35:42 UTC  
> Updated at: 2018-06-06 21:50:15 UTC  
> Url: https://github.com/boostorg/asio/issues/117#issuecomment-395221614  

No, I never said that, they are completely different.  That's the point.  
  
`CompletionCondition` is an existing concept which is used for the `read` methods, while `MatchCondition` is used by the `read_until` methods.  My proposal is to allow `CompletionCondition` (or just a simple length, if the flexibility of `CompletionCondition` doesn't seem sufficiently useful) to be used for `read_until` in addition, with semantics matching those that `read_until` already uses (namely allowing consumption from the existing buffer, rather than requiring only new bytes as `read` does).  
  
Re-read what I said above more carefully.

---

## Comment 5

> Username: uecasm  
> Created at: 2018-06-06 21:46:32 UTC  
> Url: https://github.com/boostorg/asio/issues/117#issuecomment-395224455  

To put it another way, after you have used `read_until` with some terminator (using one of the value overloads or using a `MatchCondition`) -- this will naturally read additional unconsumed bytes beyond the terminator into the streambuf -- you can then follow it up by using `read_until` again but with a specific *size* (not a terminator) and it will then read exactly that many bytes, using both existing streambuf data and if needed additional network data.  (Unlike `read`, which will _only_ use network data.)  
  
Think of a protocol somewhat like HTTP (although less complex) where the header information is indeterminate length but has a definite terminator, and part of the header information defines the precise length of the content bytes that follow.  You want to `read_until` the terminator, then `read` the exact bytes -- except that the latter doesn't work because you've already read some of the content into the streambuf, and `read` doesn't take that into account.  
  
Rather than requiring every application to consider this and implement the same workarounds over and over, the idea is to provide a simple extension to `read_until` (which already has "read from the streambuf" semantics, unlike `read`) so that you first `read_until` the terminator and then `read_until` the specific number of content bytes, and now you have the correct amount of data.  
  
(This still won't guarantee that you could use `read` safely on a subsequent message -- the first `read_until` might have read two complete messages into the streambuf, for example.  But it does mean that you can now safely only use `read_until` even when you have a mix of data-terminated and length-terminated data.)

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-06-06 21:47:53 UTC  
> Url: https://github.com/boostorg/asio/issues/117#issuecomment-395224786  

Ah I see what you mean!

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-06-08 21:59:47 UTC  
> Url: https://github.com/boostorg/asio/issues/117#issuecomment-395903292  

One problem is that CompletionCondition is nullary function while MatchCondition is a binary function. If you try to combine them then you incur the penalty of constructing two `buffers_iterator` needlessly for the case where the user just wants a completion condition.

---

## Comment 8

> Username: uecasm  
> Created at: 2018-06-09 01:17:04 UTC  
> Url: https://github.com/boostorg/asio/issues/117#issuecomment-395928962  

I suggested adding an overload.  That's the opposite of combining them.

---

## Comment 9

> Username: ghost  
> Created at: 2020-12-30 00:51:38 UTC  
> Url: https://github.com/boostorg/asio/issues/117#issuecomment-752289586  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#621](https://github.com/chriskohlhoff/asio/issues/621).
