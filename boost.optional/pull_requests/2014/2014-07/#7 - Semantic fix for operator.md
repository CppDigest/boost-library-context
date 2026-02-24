# #7 Semantic fix for operator<< [Closed]

> Username: alnr  
> Created at: 2014-07-24 21:44:39 UTC  
> Updated at: 2014-07-24 23:29:01 UTC  
> Closed at: 2014-07-24 23:29:01 UTC  
> Url: https://github.com/boostorg/optional/pull/7  

operator<<(std::ostream&, optional<T> const& o) should do nothing if "o" is not engaged (has no value), instead of printing ' --'.

---

## Comment 1

> Username: fcacciola  
> Created_at: 2014-07-24 21:55:37 UTC  
> Url: https://github.com/boostorg/optional/pull/7#issuecomment-50083739  

Actually, NO.  
This is like this by design. It is neccesary in order to properly support deserialization.  
Notice that when engaged, the out start with a whitespace.  
So, when de-serializing, the first char in the stream is peeked and depending on whether it is ' ' or '-' it returns an engaged or disengaged optional.  
  
Of course, this behavior is just a choice out of many, equally valid, which is why these operators are defined in a separate header.  
If you need a different behavior, just don't include <optional_io.hpp> and define you own.

---

## Comment 2

> Username: alnr  
> Created_at: 2014-07-24 23:29:01 UTC  
> Url: https://github.com/boostorg/optional/pull/7#issuecomment-50091571  

Thanks for explaining. Intuitively, I would not expect this behavior.

---
