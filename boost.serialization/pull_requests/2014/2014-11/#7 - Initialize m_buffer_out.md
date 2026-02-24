# #7 Initialize m_buffer_out [Closed]

> Username: tempoz  
> Created at: 2014-11-11 19:46:43 UTC  
> Updated at: 2017-05-28 19:33:00 UTC  
> Closed at: 2017-05-28 19:33:00 UTC  
> Url: https://github.com/boostorg/serialization/pull/7  

The constructors for transform_width were not initializing m_buffer_out. This was uncovered by Coverity as issues CID60662 and CID54443.

---

## Comment 1

> Username: pepsiman  
> Created_at: 2015-03-03 17:13:27 UTC  
> Url: https://github.com/boostorg/serialization/pull/7#issuecomment-76989030  

Default initialization of a character does nothing, but line 116 is performing value initialization which sets it to zero.

---

## Comment 2

> Username: tempoz  
> Created_at: 2015-03-05 16:05:57 UTC  
> Url: https://github.com/boostorg/serialization/pull/7#issuecomment-77391658  

Added commit to initialize character to zero; thank you for your help with this issue!

---

## Comment 3

> Username: pepsiman  
> Created_at: 2015-03-06 11:08:05 UTC  
> Url: https://github.com/boostorg/serialization/pull/7#issuecomment-77543040  

I mean, the empty brackets are "value initialization", and set chars (or ints, pointers, etc) to zero.

---

## Comment 4

> Username: tempoz  
> Created_at: 2015-03-06 16:33:02 UTC  
> Url: https://github.com/boostorg/serialization/pull/7#issuecomment-77588881  

Yeah, sorry pepsiman, I misread your comment and didn't remember the exact rules on value initialization. Upon re-reading them, I am fairly sure the more flexible solution (that will work regardless of the type of m_buffer_out) are the empty brackets, so i will revert the last commit. Thanks!  
  
For any curious, the reference I am using on value initialization can be found here: http://en.cppreference.com/w/cpp/language/value_initialization

---
