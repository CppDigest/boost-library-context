# #127 - Request for I-JSON (RFC 7493) Parsing Mode [Open]

> Username: jayeshbadwaik  
> Created at: 2020-07-15 03:55:36 UTC  
> Updated at: 2021-03-04 05:37:01 UTC  
> Url: https://github.com/boostorg/json/issues/127  

I was wondering if there is any interest in implementing the I-JSON parsing mode for boost.json. [I-JSON](https://tools.ietf.org/html/rfc7493) is an interoperable version of JSON which aims to remove some ambiguities in the parsing. The changes required for such a compliance are listed in [Section 2](https://tools.ietf.org/html/rfc7493#section-2) of the document.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-07-16 00:06:01 UTC  
> Url: https://github.com/boostorg/json/issues/127#issuecomment-659077957  

It is very likely that we already follow these rules. If so, we should document it. If not, we should consider whether to follow it.

---

## Comment 2

> Username: sdkrystian  
> Created at: 2020-08-10 19:58:48 UTC  
> Url: https://github.com/boostorg/json/issues/127#issuecomment-671557868  

The only normative requirement we don't comply with is checking for duplicate object key. There is some normative encouragements in there that we don't strictly follow, such as limiting precision.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-08-10 20:33:50 UTC  
> Url: https://github.com/boostorg/json/issues/127#issuecomment-671575766  

We never emit JSON with duplicates, as the DOM (via `json::object`) does not allow it. When we parse JSON, I believe we always take the first value if there are duplicate keys

---

## Comment 4

> Username: jayeshbadwaik  
> Created at: 2020-08-11 05:08:08 UTC  
> Url: https://github.com/boostorg/json/issues/127#issuecomment-671727935  

The restriction of allowing only a single object key is during the parsing, not emission.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-08-11 13:27:00 UTC  
> Url: https://github.com/boostorg/json/issues/127#issuecomment-671946053  

When an object is constructed during parsing, we check its elements for duplicates here:  
https://github.com/CPPAlliance/json/blob/ec29dfa768a16672534f758005dfe4841a8ea93d/include/boost/json/detail/impl/object_impl.ipp#L79  
The duplicates are removed by replacing the duplicate with the last element in the object array, and then shrinking the array size by 1.

---

## Comment 6

> Username: sdkrystian  
> Created at: 2020-08-11 22:23:35 UTC  
> Url: https://github.com/boostorg/json/issues/127#issuecomment-672321582  

@vinniefalco To conform to I-JSON we would have the check for duplicate keys within `basic_parser`, not the handler.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2020-08-11 22:26:00 UTC  
> Url: https://github.com/boostorg/json/issues/127#issuecomment-672323500  

Fuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuuck

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-08-11 22:27:41 UTC  
> Url: https://github.com/boostorg/json/issues/127#issuecomment-672324265  

`basic_parser` can never do such a thing, so I don't think we can claim I-JSON support for it. But that's okay, because we _can_ claim I-JSON support in `parser` which is what 99% of users care about. For the other 1% of users who are implementing their own parser by using `basic_parser` directly, I think it is reasonable to expect that if they want I-JSON compliance, that can be responsible for it. How do you feel about that @jayeshbadwaik ?

---

## Comment 9

> Username: jayeshbadwaik  
> Created at: 2020-08-12 05:08:37 UTC  
> Updated at: 2020-08-12 05:09:29 UTC  
> Url: https://github.com/boostorg/json/issues/127#issuecomment-672577368  

One way to implement that in your codebase I see is to throw in case of duplicate in here: https://github.com/CPPAlliance/json/blob/ec29dfa768a16672534f758005dfe4841a8ea93d/include/boost/json/detail/impl/object_impl.ipp#L79  
  
And handle that exception upward to report that the provided I-JSON document is ill-formed. This will require adding an option to parse_options to allow the user to parse in "I-JSON" mode and require you to remove `noexcept` qualifer for `object_impl.build()`. Since I am not aware of how the rest of the code works, I wonder if that is palatable.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2020-09-15 14:17:01 UTC  
> Url: https://github.com/boostorg/json/issues/127#issuecomment-692745077  

Actually, when we detect a duplicate we can set a bit somewhere in the `object` and then in `parser` we can query new objects to see if there's a duplicate and throw as needed.

---

## Comment 11

> Username: jayeshbadwaik  
> Created at: 2020-09-16 01:27:23 UTC  
> Url: https://github.com/boostorg/json/issues/127#issuecomment-693115250  

That can work too, yeah.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2021-03-04 05:36:50 UTC  
> Url: https://github.com/boostorg/json/issues/127#issuecomment-790307317  

We should target this for the middle of year release?
