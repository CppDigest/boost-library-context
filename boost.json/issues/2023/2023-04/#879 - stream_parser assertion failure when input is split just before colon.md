# #879 - stream_parser assertion failure when input is split just before colon [Closed]

> Username: mjerabek  
> Created at: 2023-04-18 15:26:40 UTC  
> Updated at: 2023-04-18 22:59:41 UTC  
> Closed at: 2023-04-18 22:59:41 UTC  
> Url: https://github.com/boostorg/json/issues/879  

When the input for `stream_parser` is split in a particular way, an assertion is triggered inside of the library. On other occasions, I get "syntax error", although the JSON is perfectly valid. Note that the included reproducer is reduced to triggering the assert (and I hope the syntax error is just a different manifestation of the same bug).  
  
**Note**: this happens only on gcc and only in specific optimization levels - `-O0`/`-O1` trigger the bug, while with `-O2`/`-O3` the assertion is not triggered (which is IMHO rather weird).  
  
Further oddities are described in the reproducer.  
  
### Version of Boost  
  
1.81.0  
  
### Steps necessary to reproduce the problem  
  
Fortunately it's reproducible on compiler explorer: https://godbolt.org/z/Ea3a5zvvP

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-04-18 15:40:16 UTC  
> Url: https://github.com/boostorg/json/issues/879#issuecomment-1513382916  

This is almost certainly a duplicate of #828 . Can you test if the problem goes away with Boost 1.82?

---

## Comment 2

> Username: mjerabek  
> Created at: 2023-04-18 22:59:41 UTC  
> Url: https://github.com/boostorg/json/issues/879#issuecomment-1513888730  

Thank you! I didn't know 1.82 was already out, plus I forgot to check already closed issues :facepalm: .  
  
Works with both 1.82 and 1.81 with the patch backported.
