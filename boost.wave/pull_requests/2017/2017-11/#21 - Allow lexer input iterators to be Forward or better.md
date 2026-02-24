# #21 Allow lexer input iterators to be Forward or better [Merged]

> Username: jefftrull  
> Created at: 2017-11-22 17:17:58 UTC  
> Updated at: 2017-11-23 17:36:26 UTC  
> Merged at: 2017-11-23 17:36:26 UTC  
> Closed at: 2017-11-23 17:36:26 UTC  
> Url: https://github.com/boostorg/wave/pull/21  

This change is implemented primarily by templating the Scanner class, plus some related functions, on the lexer IteratorT and using that type for three Scanner data members.  
  
In addition, we make use of std::distance/std::advance where possible so we don't lose performance on RandomAccess inputs.  
  
If merged this will resolve TRAC 12208.

---

## Comment 1

> Username: hkaiser  
> Created_at: 2017-11-22 18:49:01 UTC  
> Url: https://github.com/boostorg/wave/pull/21#issuecomment-346441254  

Thanks Jeff! I'm not sure however how this patch will solve the issue you're referencing. Could you explain your reasoning, please?

---

## Comment 2

> Username: jefftrull  
> Created_at: 2017-11-22 21:50:44 UTC  
> Url: https://github.com/boostorg/wave/pull/21#issuecomment-346482619  

The current code expects the iterators supplied to the lexer to be RandomAccess.  The fundamental reason, IMO, is that the input range is assumed by the code to be laid out consecutively in memory.  For example [here](https://github.com/boostorg/wave/blob/develop/include/boost/wave/cpplexer/re2clex/cpp_re2c_lexer.hpp#L129). In general the input range is assumed to be compatible with `unsigned char *`.  
  
This PR resolves the issue by generalizing the three pointers handling the input range (namely `first`, `act`, and `last`) to an iterator of category Forward or higher, replacing pointer-style access with operations compatible with such iterators.  The remainder of the operations (including buffering and EOL detection) continue to use `uchar *` as before.  
  
The changes pass the test case supplied in the issue, as well as all Wave unit tests.

---

## Review 3 [Commented]

> Username: hkaiser  
> Created_at: 2017-11-23 17:36:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/21#pullrequestreview-78778288  

LGTM, thanks a lot!

---
