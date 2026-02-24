# #113 - Constructing UUIDs from 2 64 bit integers [Closed]

> Username: karlsosha  
> Created at: 2020-04-01 18:22:28 UTC  
> Updated at: 2025-09-04 00:45:31 UTC  
> Closed at: 2025-09-04 00:45:31 UTC  
> Url: https://github.com/boostorg/uuid/issues/113  

As opened question in [stackoverflow](https://stackoverflow.com/questions/60958645/boostuuid-setting-specific-value) suggests it is occasionally highly useful to create UUIDs from a pair of 64 bit integers.  
  
Java provides the mechanism to do so:   
  
 `UUID test_uuid(long1, long2);`  
  
It would be very helpful to be able to do the same with boost::uuid

---

## Comment 1

> Username: Lastique  
> Created at: 2020-04-01 18:46:59 UTC  
> Url: https://github.com/boostorg/uuid/issues/113#issuecomment-607427069  

As noted in the [answer](https://stackoverflow.com/a/60967500/4636534) on that SO question, I'm opposed to having such a constructor, because it is confusing as to what UUID is the result of such construction. Given the differences in endianness, either users or the UUID constructor would have to convert endianness, neither of which is a good solution.  
  
Just use initialization from bytes or strings.

---

## Comment 2

> Username: Lastique  
> Created at: 2020-04-01 18:54:50 UTC  
> Url: https://github.com/boostorg/uuid/issues/113#issuecomment-607430938  

For reference, [P0959R3](https://github.com/mariusbancila/stduuid/blob/master/P0959.md) currently does not provide such a constructor for `std::uuid`.

---

## Comment 3

> Username: karlsosha  
> Created at: 2020-04-01 19:40:59 UTC  
> Url: https://github.com/boostorg/uuid/issues/113#issuecomment-607451935  

Technically you don't have to perform endianness conversion in order to construct the byte array:  
  
```  
for(size_t i = 0 ; i < 8 ; ++i) {  
   upper_byte = (uint8_t) ((upper_64 >> (i*8)) && 0xff);  
   lower_byte = (uint8_t) ((lower_64 >>(i*8)) && 0xff);  
   uuid_bytes[i] = lower_byte;   
   uuid_bytes[i+8] = upper_byte;  
}  
```  
  
Granted that constructing with bytes is faster and constructing from String is necessary but given the existing code base out there constructing from a pair of 64-bit integers is very useful.

---

## Comment 4

> Username: Lastique  
> Created at: 2020-04-01 19:58:41 UTC  
> Updated at: 2020-04-02 10:37:52 UTC  
> Url: https://github.com/boostorg/uuid/issues/113#issuecomment-607460161  

The loop you presented is, effectively, endianness conversion. And on little-endian machines it gives the result that is different from the natural byte order. Because of the endianness issue, two 64-bit integers are a bad choice to represent a 128-bit UUID as a binary blob.  
  
Also, as defined by [RFC4122](https://tools.ietf.org/html/rfc4122#section-4.1.2), UUID is not a pair of 64-bit integers, it has a number of fields with certain requirements on them. So the constructor from 64-bit integers would not represent the actual UUID structure.  
  
I think, if a code base is constructing UUIDs from a pair of integers, that is not a good practice, and we should not advertise it.

---

## Comment 5

> Username: jeking3  
> Created at: 2022-02-04 20:23:11 UTC  
> Url: https://github.com/boostorg/uuid/issues/113#issuecomment-1030322514  

You can make your own subclass which provides this functionality by writing into the bytes; it doesn't conform to the RFC specification so I am going to close this.

---

## Comment 6

> Username: sutambe  
> Created at: 2023-10-04 21:24:14 UTC  
> Url: https://github.com/boostorg/uuid/issues/113#issuecomment-1747663071  

boost uuid uses big-endian storage format (regardless of what machine type it's running on)  
The following code works on a little endian machine.  
  
```  
struct UUID {  
  boost::uuids::uuid id;  
  UUID(uint64_t lower64, uint64_t higher64) {  
    for (size_t i = 0; i < 8; ++i) {  
      id.data[i] = (higher64 >> ((7 - i) * 8)) & 0xFF;  
      id.data[i + 8] = (lower64 >> ((7 - i) * 8)) & 0xFF;  
    }  
  }  
}  
```

---

## Comment 7

> Username: pdimov  
> Created at: 2024-04-22 15:05:44 UTC  
> Url: https://github.com/boostorg/uuid/issues/113#issuecomment-2069805300  

RFC 4122 is unambiguous that a UUID is big endian (stored with the Most Significant Byte first) when treated as a 128 bit integer, so I think the Java constructor `UUID(uint64_t highPart, uint64_t lowPart)` got it right, and we might consider adding it.  
  
A constructor from `__uint128_t` (where available) might also make sense.

---

## Comment 8

> Username: jeking3  
> Created at: 2025-07-03 21:20:17 UTC  
> Url: https://github.com/boostorg/uuid/issues/113#issuecomment-3033683924  

If we do this then max_generator can be optimized to use the 64-bit values instead of 16 8-bit values.

---

## Comment 9

> Username: Lastique  
> Created at: 2025-07-03 22:11:00 UTC  
> Url: https://github.com/boostorg/uuid/issues/113#issuecomment-3033819409  

I still think this is a bad idea. The two 64-bit ints simply have no meaning wrt. the UUID. Is there a use case that necessitates using two 64-bit ints as the source of construction? Is there a reasonable means to obtain these two ints for the user, other than try to compose those ints from the desired byte representation of the UUID? In the latter case, just use the constructor from bytes, which is clear and unambiguous.
