# #822 - Feature request: (de)serialize msgpack [Open]

> Username: pfeatherstone  
> Created at: 2022-12-21 10:21:31 UTC  
> Updated at: 2022-12-27 17:32:38 UTC  
> Url: https://github.com/boostorg/json/issues/822  

Would be great if you could serialize/deserialize to/from a `json` object.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-12-27 15:21:13 UTC  
> Updated at: 2022-12-27 15:22:14 UTC  
> Url: https://github.com/boostorg/json/issues/822#issuecomment-1365978605  

We need to document our position on these other serialization formats - that is, that we do not support them nor do we intend to support them. And we should explain why. We could offer serializers for other formats like MSGPACK as example programs - not part of the public API of the library itself, and without a corresponding parser.

---

## Comment 2

> Username: pfeatherstone  
> Created at: 2022-12-27 15:34:39 UTC  
> Url: https://github.com/boostorg/json/issues/822#issuecomment-1365988982  

It's not immediately obvious to me. Making this work with Beast would be awesome. I don't want to send massive stringy JSON objects to the browser. Msgpack is way better

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-12-27 17:32:38 UTC  
> Url: https://github.com/boostorg/json/issues/822#issuecomment-1366065613  

Compress the JSON using gzip
