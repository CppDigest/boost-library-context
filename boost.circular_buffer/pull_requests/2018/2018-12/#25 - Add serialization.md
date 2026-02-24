# #25 Add serialization [Closed]

> Username: yuvalif  
> Created at: 2018-12-18 12:59:54 UTC  
> Updated at: 2022-07-12 14:10:22 UTC  
> Closed at: 2022-07-12 14:10:22 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/25  

Adding circular buffer serialization (including the space optimized version). This is to address issue #24.  
For performance reasons the serialization of the buffer itself is done in, bulk, in binary format, and not element by element.

---

## Review 1 [Changes requested]

> Username: jeking3  
> Created_at: 2019-02-25 20:55:08 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/circular_buffer/pull/25#pullrequestreview-207625892  

Licensing issues...

📁 example/circular_buffer_serialization.cpp

```diff
   1 |+ #include <boost/circular_buffer.hpp>
```

> Username: jeking3  
> Created_at: 2019-02-25 20:52:26 UTC  
> Updated_at: 2019-03-04 10:08:07 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/25#discussion_r260012914  

Should still have a boost license header as it is redistributed.


📁 example/jamfile.v2

```diff
  13 | 			<library>/boost/system//boost_system
  14 | 			<library>/boost/thread//boost_thread
  15 |+ 			<library>/boost//serialization
```

> Username: jeking3  
> Created_at: 2019-02-25 20:53:03 UTC  
> Updated_at: 2019-03-04 10:08:07 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/25#discussion_r260013119  

This doesn't match the others - is it correct?

> Username: yuvalif  
> Created_at: 2019-02-26 09:41:26 UTC  
> Updated_at: 2019-03-04 10:08:07 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/25#discussion_r260199580  

you mean the library location? i don't know why it is different, but this is the correct one for serialization


📁 include/boost/circular_buffer/serialization.hpp

```diff
   1 |+ // boost::circular_buffer serialization
```

> Username: jeking3  
> Created_at: 2019-02-25 20:53:21 UTC  
> Updated_at: 2019-03-04 10:08:07 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/25#discussion_r260013222  

Needs a boost license header.


---

## Comment 2

> Username: glenfe  
> Created_at: 2019-02-25 21:07:37 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/25#issuecomment-467183394  

This would make circular_buffer have a dependency on serialization which I'm not sure we want.

---

## Comment 3

> Username: jeking3  
> Created_at: 2019-02-26 01:35:10 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/25#issuecomment-467256127  

It would be easy enough to include macro guards to disable it entirely if someone so desired...

---

## Comment 4

> Username: yuvalif  
> Created_at: 2019-02-26 09:28:20 UTC  
> Updated_at: 2019-02-26 09:29:04 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/25#issuecomment-467364061  

@glenfe  @jeking3 , in multi_index it was done under: ```BOOST_MULTI_INDEX_DISABLE_SERIALIZATION```  
will follow the same pattern here

---

## Comment 5

> Username: pdimov  
> Created_at: 2019-02-26 16:15:35 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/25#issuecomment-467503433  

The tests only test a completely full buffer consisting of one span. In general, an archive does not allow two save_binary calls on save to be loaded with one load_binary.  
  
Using binary serialization is questionable anyway. This only works for trivially copyable types, and the code doesn't check that; and text/xml archives are supposed to be portable, and dumping binary into them defeats their purpose. This implementation is only suitable for use with the nonportable binary archive.

---

## Comment 6

> Username: yuvalif  
> Created_at: 2019-02-26 17:45:10 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/25#issuecomment-467539933  

@pdimov   
> The tests only test a completely full buffer consisting of one span.  
  
Fragmented buffer was tested in the examples, will add to the unit test.  
> In general, an archive does not allow two save_binary calls on save to be loaded with one load_binary.  
  
Didn't want to use serialize,to avoid changes to the buffer. Can change to 2 separate calls of load_binary. BTW, why is there an issue with 2 calls?  
  
In general binary serialization was used for performance reasons, but I do see the issues with it. Will modify the implementation archive the elements one by one.  
  
There is probably a way to specialize for the case that the archive is binary anyway - will look into that.

---

## Comment 7

> Username: pdimov  
> Created_at: 2019-02-26 17:55:28 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/25#issuecomment-467543920  

> BTW, why is there an issue with 2 calls?  
  
Well, suppose that some XML archive implements `save_binary` as `<binary>04A3719F...</binary>`. The two save_binary calls will emit two such sequences. Then the load_binary call will only load the first one, and it won't have enough data inside.

---

## Comment 8

> Username: yuvalif  
> Created_at: 2019-03-04 09:13:16 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/25#issuecomment-469175276  

- added disabling macro: ``` BOOST_CIRCULAR_BUFFER_DISABLE_SERIALIZATION``` for the feature. @glenfe @jeking3 does it needs to be documented somewhere?  
- added test for non-linearized buffer (which actually found a bug...)  
- basic implementation serialize the elements one by one, added specialized implementation for the binary archive case. @pdimov please let me know if you still see issue with this optimization - if this is becoming too complex I can remove (note that there is a big difference in performance, shown in the example)  
- note that XML archive is not supported, as I don't use NVP serialization. Don't think this is mandatory in the circular buffer case. And could be added later

---

## Comment 9

> Username: jeking3  
> Created_at: 2019-03-05 18:25:27 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/25#issuecomment-469795466  

Documentation is useful, so if you can add it to the docs (BOOST_CIRCULAR_BUFFER_DISABLE_SERIALIZATION), I think that's best.

---
