# #342 - CBOR Research [Closed]

> Username: vinniefalco  
> Created at: 2020-09-15 14:40:57 UTC  
> Updated at: 2024-04-22 21:23:38 UTC  
> Closed at: 2020-09-20 20:45:03 UTC  
> Url: https://github.com/boostorg/json/issues/342  

We need preliminary research into CBOR. Is the mapping to and from a `json::value` clean? Are there any obstacles to implementing a parser and serializer for CBOR? This is research-only, no implementation.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-15 14:44:07 UTC  
> Url: https://github.com/boostorg/json/issues/342#issuecomment-692762829  

https://cbor.io/spec.html  
  
There are extensible "tags", which might not map: https://www.iana.org/assignments/cbor-tags/cbor-tags.xhtml

---

## Comment 2

> Username: rainerdeyke  
> Created at: 2020-09-16 09:22:43 UTC  
> Url: https://github.com/boostorg/json/issues/342#issuecomment-693285272  

A CBOR implementation is not expected to know all semantic tags (and generally cannot, since new semantic tags can be added at any time).  It is perfectly valid to ignore all semantic tags completely, and this is explicitly stated in the CBOR spec.  Section 2.4, paragraph 3: "Understanding the semantic tags is optional for a decoder; it can just jump over the initial bytes of the tag and interpret the tagged data item itself."  This seems like the best approach for Boost.JSON, since none of the types used by JSON require semantic tags.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-09-16 14:08:26 UTC  
> Url: https://github.com/boostorg/json/issues/342#issuecomment-693430659  

> The one interesting decision that needs to be made here is how to handle CBOR byte strings (major type 3), as they aren't representable in JSON or in the current boost::json::value.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-09-20 20:45:03 UTC  
> Url: https://github.com/boostorg/json/issues/342#issuecomment-695834147  

Well Peter did this

---

## Comment 5

> Username: ecorm  
> Created at: 2024-04-22 21:23:36 UTC  
> Url: https://github.com/boostorg/json/issues/342#issuecomment-2070976580  

> A CBOR implementation is not expected to know all semantic tags (and generally cannot, since new semantic tags can be added at any time). It is perfectly valid to ignore all semantic tags completely, and this is explicitly stated in the CBOR spec. Section 2.4, paragraph 3: "Understanding the semantic tags is optional for a decoder; it can just jump over the initial bytes of the tag and interpret the tagged data item itself." This seems like the best approach for Boost.JSON, since none of the types used by JSON require semantic tags.  
  
Sorry for the late comment. There are a number of CBOR tags where it's impossible to reconstitute the value without knowing the tag. For example, tags 2 (unsigned bignum) and 3 (negative bignum). I've pointed out this flaw years ago on the CBOR mailing list, yet new tags keep popping up where part of the information (beyond semantics) is encoded in the tag. They are too obsessed over saving every possible byte, and don't seem care about being able to transcode to JSON without losing unrecoverable information.
