# #1061 - websocket::detail::mask_inplace() needs a test for split message [Closed]

> Username: vchang-akamai  
> Created at: 2018-03-09 00:43:41 UTC  
> Updated at: 2018-08-04 12:25:05 UTC  
> Closed at: 2018-08-04 12:25:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1061  

I stumbled across a weird websocket message corruption issue, and I think there might be a bug in websocket::detail::mask_inplace(). I'm using top of develop branch.  
  
It was triggered when my server was reading an incoming message that was fragmented in multiple parts. The first part was 291 bytes, which when modulo'd 4, is 3.    
  
This first fragment was masked properly, but subsequent fragments were mangled. (I also had another nearly identical incoming message, but it was fragmented differently and was not mangled.)  
  
The original mask key was: b7 00 bb 51  
  
The last 3 bytes were XOR'd against b7 00 bb.  Therefore, the next fragment should be masked starting with 51, so the modified key should be 51 b7 00 bb.  
  
However, instead, the modified key was 00 bb 51 b7  
  
I made this modification:  
  
    {  
        for(std::size_t i = 0; i < n; ++i)  
            p[i] ^= mask[i];  
        rol(key, /*4 - */ n);  
    }  
  
and it seemed to work.  Or I suppose you could also substitue rol() with ror().

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-03-09 01:38:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1061#issuecomment-371684596  

This code is supposed to work on both big-endian and little-endian platforms. I am investigating.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-03-09 15:50:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1061#issuecomment-371851590  

`rol(key, n)` is correct, thanks for finding this (and just before a release!).  
  
I'll keep this issue open as a reminder to add a test for a split message.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2018-04-24 16:52:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1061#issuecomment-384003551  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2018-05-29 01:14:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1061#issuecomment-392626316  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2018-06-28 04:38:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1061#issuecomment-400908508  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: stale[bot]  
> Created at: 2018-07-28 12:08:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1061#issuecomment-408603814  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: stale[bot]  
> Created at: 2018-08-04 12:25:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1061#issuecomment-410445961  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
