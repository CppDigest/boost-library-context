# #183 Fix UTF-8 validation [Closed]

> Username: sdkrystian  
> Created at: 2020-08-21 23:01:08 UTC  
> Updated at: 2020-08-22 14:17:52 UTC  
> Closed at: 2020-08-22 14:17:52 UTC  
> Url: https://github.com/boostorg/json/pull/183  

fixes #162

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-21 23:49:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/183#pullrequestreview-472869317  

📁 include/boost/json/basic_parser.hpp

```diff
1297 |-             goto do_str1;
1005 |+             seq_.save(cs.begin(), cs.remain());
1006 |+             if(BOOST_JSON_UNLIKELY(seq_.complete()))
```

> Username: vinniefalco  
> Created_at: 2020-08-21 23:49:07 UTC  
> Updated_at: 2020-08-22 13:53:29 UTC  
> Url: https://github.com/boostorg/json/pull/183#discussion_r475013576  

this looks like a bug, why would being complete be an error?

> Username: sdkrystian  
> Created_at: 2020-08-22 13:15:01 UTC  
> Updated_at: 2020-08-22 13:53:29 UTC  
> Url: https://github.com/boostorg/json/pull/183#discussion_r475089321  

Nope, `count_value<false>` will skip valid UTF-8, meaning that any complete points we have at that point are invalid.


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-21 23:49:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/183#pullrequestreview-472869361  

📁 include/boost/json/basic_parser.hpp

```diff
1012 |             // flush unescaped run from input
1302 |-             if(BOOST_JSON_LIKELY(cs.begin() > start))
1013 |+             if(BOOST_JSON_LIKELY(size))
```

> Username: vinniefalco  
> Created_at: 2020-08-21 23:49:24 UTC  
> Updated_at: 2020-08-22 13:53:29 UTC  
> Url: https://github.com/boostorg/json/pull/183#discussion_r475013615  

I prefer `size > 0`

> Username: sdkrystian  
> Created_at: 2020-08-22 13:15:59 UTC  
> Updated_at: 2020-08-22 13:53:29 UTC  
> Url: https://github.com/boostorg/json/pull/183#discussion_r475089408  

Meh, I just use `size` because it maps 1:1 with the instruction in the actual codegen


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-21 23:51:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/183#pullrequestreview-472869752  

📁 include/boost/json/detail/utf8.hpp

```diff
  53 |+     };
  54 |+     return first[static_cast<
  55 |+         unsigned char>(c)];
```

> Username: vinniefalco  
> Created_at: 2020-08-21 23:51:28 UTC  
> Updated_at: 2020-08-22 13:53:29 UTC  
> Url: https://github.com/boostorg/json/pull/183#discussion_r475014030  

unnecessary line break, you need to pay attention to your surroundings


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-21 23:51:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/183#pullrequestreview-472869778  

📁 include/boost/json/detail/utf8.hpp

```diff
  58 |+ inline
  59 |+ bool
  60 |+ valid_utf8(const char* p, uint16_t first)
```

> Username: vinniefalco  
> Created_at: 2020-08-21 23:51:39 UTC  
> Updated_at: 2020-08-22 13:53:29 UTC  
> Url: https://github.com/boostorg/json/pull/183#discussion_r475014061  

`is_valid_utf8`


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-21 23:51:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/183#pullrequestreview-472869818  

📁 include/boost/json/detail/utf8.hpp

```diff
  61 |+ {
  62 |+     // KRYSTIAN TODO: account for big endian
  63 |+     // also, make a convinence function for
```

> Username: vinniefalco  
> Created_at: 2020-08-21 23:51:50 UTC  
> Updated_at: 2020-08-22 13:53:29 UTC  
> Url: https://github.com/boostorg/json/pull/183#discussion_r475014128  

spelling: convenience


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-21 23:52:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/183#pullrequestreview-472869935  

📁 include/boost/json/detail/utf8.hpp

```diff
 104 |+ private:
 105 |+     char seq_[4];
 106 |+     uint16_t first_;
```

> Username: vinniefalco  
> Created_at: 2020-08-21 23:52:30 UTC  
> Updated_at: 2020-08-22 13:53:29 UTC  
> Url: https://github.com/boostorg/json/pull/183#discussion_r475014233  

why 16 bit?

> Username: sdkrystian  
> Created_at: 2020-08-22 13:18:30 UTC  
> Updated_at: 2020-08-22 13:53:29 UTC  
> Url: https://github.com/boostorg/json/pull/183#discussion_r475089617  

We store the size of the sequence within the low byte, and both bytes together determine the "classification" of the sequence (i.e. what following bytes are valid)


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-21 23:59:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/183#pullrequestreview-472871204  

📁 include/boost/json/detail/utf8.hpp

```diff
 114 |+         first_ = classify_utf8(*p & 0x7F);
 115 |+         size_ = remain >= length() ? 
 116 |+             length() : static_cast<uint8_t>(remain);
```

> Username: vinniefalco  
> Created_at: 2020-08-21 23:59:21 UTC  
> Updated_at: 2020-08-22 13:53:29 UTC  
> Url: https://github.com/boostorg/json/pull/183#discussion_r475015415  

No more ternaries please!!


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-22 00:00:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/183#pullrequestreview-472871325  

📁 include/boost/json/detail/utf8.hpp

```diff
 139 |+         if(remain >= needed())
 140 |+         {
 141 |+             std::memcpy(+seq_ + size_, p, needed());
```

> Username: vinniefalco  
> Created_at: 2020-08-22 00:00:05 UTC  
> Updated_at: 2020-08-22 13:53:29 UTC  
> Url: https://github.com/boostorg/json/pull/183#discussion_r475015525  

why `+seq_`? That's confusing


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-22 00:00:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/183#pullrequestreview-472871389  

📁 include/boost/json/detail/utf8.hpp

```diff
 149 |+ 
 150 |+     const char*
 151 |+     sequence() const noexcept
```

> Username: vinniefalco  
> Created_at: 2020-08-22 00:00:25 UTC  
> Updated_at: 2020-08-22 13:53:29 UTC  
> Url: https://github.com/boostorg/json/pull/183#discussion_r475015594  

what does the word `sequence` mean? Shouldn't this be `data()`?

> Username: sdkrystian  
> Created_at: 2020-08-22 13:19:42 UTC  
> Updated_at: 2020-08-22 13:53:29 UTC  
> Url: https://github.com/boostorg/json/pull/183#discussion_r475089709  

It's a UTF-8 byte sequence, so `sequence` gives us the actual bytes


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-22 00:00:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/183#pullrequestreview-472871479  

📁 test/basic_parser.cpp

```diff
1231 |         bad("\"\\n\xf4\x80\xbf\x70-\\n\xf4\x80\xbf\x70\"");
1232 |+ 
1233 |+         class utf8_parser
```

> Username: vinniefalco  
> Created_at: 2020-08-22 00:00:53 UTC  
> Updated_at: 2020-08-22 13:53:29 UTC  
> Url: https://github.com/boostorg/json/pull/183#discussion_r475015686  

I think you could just use the regular `parser` and inspect the resulting `string`.


---
