# #29 - Add stream insertion operator? [Closed]

> Username: reddwarf69  
> Created at: 2021-09-01 11:41:59 UTC  
> Updated at: 2021-11-03 14:58:05 UTC  
> Closed at: 2021-11-03 14:58:05 UTC  
> Url: https://github.com/boostorg/variant2/issues/29  

boost::variant has a stream insertion operator (https://www.boost.org/doc/libs/1_77_0/doc/html/variant/reference.html#header.boost.variant.variant_hpp). I don't know why it was not included in std::variant, but people are having problems with that (https://cpplang.slack.com/messages/C21PKDHSL/p1586635254026300).  
  
I'm not sure if defining it myself in boost::variant2 is as bad as defining it in std, but unless there is a reason not to adding a stream insertion operator to boost::variant2 could make people lives easier.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-09-01 13:08:32 UTC  
> Url: https://github.com/boostorg/variant2/issues/29#issuecomment-910268654  

Good idea, thanks. Intuitively it seems at least as legitimate to me to make it output the index and the value, instead of just the value itself; this way, when one outputs `variant<int, float, string>`, the result will be `0:7`, `1:7` and `2:7` respectively for `7`, `7.0f` and `"7"`, instead of `7` in all three cases. But since Variant1 chooses the latter (`7`), there're probably both reasons for, and existing expectations of, it.  
  
@madmongo1 you're being quoted as a motivating example; can you please share the use case?

---

## Comment 2

> Username: reddwarf69  
> Created at: 2021-09-01 13:38:08 UTC  
> Url: https://github.com/boostorg/variant2/issues/29#issuecomment-910294506  

I only though about such an operator forwarding to the underlying type stream insertion operator. Now that you mention it... yeah, there are other legitimate options. That may be why it was not included in std::variant, and not really sure any more if boost::variant2:variant should have it.  
  
At the end of the day I guess we should all just be specializing fmt::formatter/std::formatter. But without dynamic_formatter allowing user-defined types (https://github.com/fmtlib/fmt/issues/1923#issuecomment-705140646) is not that simple either.

---

## Comment 3

> Username: pdimov  
> Created at: 2021-09-01 16:43:49 UTC  
> Url: https://github.com/boostorg/variant2/issues/29#issuecomment-910462631  

What's the reason to use `dynamic_formatter`?

---

## Comment 4

> Username: pdimov  
> Created at: 2021-09-01 17:14:54 UTC  
> Url: https://github.com/boostorg/variant2/issues/29#issuecomment-910487527  

Something like this I suppose: https://godbolt.org/z/9hz7xPvj3

---

## Comment 5

> Username: reddwarf69  
> Created at: 2021-09-01 20:19:25 UTC  
> Url: https://github.com/boostorg/variant2/issues/29#issuecomment-910701568  

Uhm... honestly I don't know. I basically had a quick look at fmt for the first time, found about dynamic_formatter, supposed there was something complicated requiring such a thing, triggered the bug and kind of gave up.  
I may just do something like that.

---

## Comment 6

> Username: pdimov  
> Created at: 2021-09-04 19:20:36 UTC  
> Url: https://github.com/boostorg/variant2/issues/29#issuecomment-913026081  

There is indeed something complicated that requires the dynamic formatter; things like https://godbolt.org/z/fYncTeE9M don't work otherwise. Parsing `.{}f` correctly and handling dynamic width/precision is considerably more involved than what I did.

---

## Comment 7

> Username: pdimov  
> Created at: 2021-09-15 00:55:18 UTC  
> Url: https://github.com/boostorg/variant2/issues/29#issuecomment-919614202  

Implemented in https://github.com/boostorg/variant2/commit/e668c099ce650dba28815ae78fdd4eaa262a8396 and https://github.com/boostorg/variant2/commit/2da13befd78382cc8c6dc69eda821fb9f2e7407c.
