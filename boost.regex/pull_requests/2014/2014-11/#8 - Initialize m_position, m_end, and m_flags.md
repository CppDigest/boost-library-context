# #8 Initialize m_position, m_end, and m_flags [Closed]

> Username: tempoz  
> Created at: 2014-11-11 18:57:57 UTC  
> Updated at: 2014-11-13 11:05:29 UTC  
> Closed at: 2014-11-13 11:05:29 UTC  
> Url: https://github.com/boostorg/regex/pull/8  

The constructor for basic_regex_formatter left m_position, m_end, and m_flags uninitialized, as uncovered by Coverity CID12310. We now value-initialize them in the initializer list.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-11-13 11:05:29 UTC  
> Url: https://github.com/boostorg/regex/pull/8#issuecomment-62875151  

Merged to develop.

---
