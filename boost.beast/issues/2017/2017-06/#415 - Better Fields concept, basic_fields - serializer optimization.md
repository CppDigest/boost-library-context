# #415 - Better Fields concept, basic_fields / serializer optimization [Closed]

> Username: vinniefalco  
> Created at: 2017-06-03 19:17:09 UTC  
> Updated at: 2017-06-12 04:00:32 UTC  
> Closed at: 2017-06-12 04:00:32 UTC  
> Url: https://github.com/boostorg/beast/issues/415  

Instead of storing two separate strings for a header field, `basic_fields` could just store the entire header in the already-serialized format. For example it could store the pair `{"User-Agent", "Beast"}` as  
**`"User-Agent: Beast\r\n"`** using a single memory buffer and a couple of `string_view`.  
  
There is another advantage to this: serialization of `basic_fields` may require no allocation at all (currently it needs a dynamic buffer to build the header). This can be accomplished by making the **Fields** concept more well defined. It can have a nested type `serializer` which is instantiated during serialization and returns a **ConstBufferSequence** containing all of the serialized headers. `basic_fields` would simply return an adapted buffer range of non-owning references.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-12 04:00:32 UTC  
> Url: https://github.com/boostorg/beast/issues/415#issuecomment-307685136  

Done!
