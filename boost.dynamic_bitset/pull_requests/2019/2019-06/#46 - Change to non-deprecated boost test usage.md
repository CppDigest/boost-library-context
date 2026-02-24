# #46 Change to non-deprecated boost test usage [Closed]

> Username: jeking3  
> Created at: 2019-06-07 10:22:49 UTC  
> Updated at: 2019-06-07 15:28:41 UTC  
> Closed at: 2019-06-07 11:06:04 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/46  

The unit tests were using boost/test/minimal which is deprecated.

---

## Comment 1

> Username: glenfe  
> Created_at: 2019-06-07 11:06:03 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/46#issuecomment-499845506  

Parts of DynamicBitset already use LightweightTest from Core, so the others should probably use it as well (instead of switching to the non-deprecated Test, which is more heavyweight). Addressed in ccb380a1bcf054d406270b74e9f5b626577d776e.

---

## Comment 2

> Username: jeking3  
> Created_at: 2019-06-07 11:11:04 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/46#issuecomment-499846738  

You committed code without running it through CI first.  Next time please open a pull request first and get build results.

---

## Comment 3

> Username: glenfe  
> Created_at: 2019-06-07 11:59:49 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/46#issuecomment-499858079  

> without running it through CI first  
  
No, just without running it through CI on the boostorg Travis queue (or the jeking3 Appveyor queue). I ran it through CI on the glenfe Travis and Appveyor queue.

---

## Comment 4

> Username: jeking3  
> Created_at: 2019-06-07 15:28:41 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/46#issuecomment-499930380  

Ok, cool - thanks.

---
