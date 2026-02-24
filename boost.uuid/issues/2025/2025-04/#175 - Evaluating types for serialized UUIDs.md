# #175 - Evaluating types for serialized UUIDs [Closed]

> Username: justend29  
> Created at: 2025-04-15 14:43:22 UTC  
> Updated at: 2025-09-22 22:30:50 UTC  
> Closed at: 2025-09-22 22:30:50 UTC  
> Url: https://github.com/boostorg/uuid/issues/175  

In many cases, UUIDs are handled directly in their serialized text representation. Examples may include:  
  
1. *Logging* - Instead of serializing a `uuid` every time, storing the serialized version and logging this is much more efficient.  
2. *Databases* - Storing UUIDs in text form in certain databases, such as DynamoDB, is often advantageous.  
3. *Serialization* - Consider JSON/XML/YAML serialization & deserialization. Parsing the text UUIDs can be avoided and is often not necessary.  
  
I deal with serialized UUIDs so frequently that I've created type-safe `Serialized_uuid` & `Serialized_uuid_view` types for my own usage. I'm wondering if the boost.uuid library authors would be interested in accepting these types - I'd be happy to contribute the code.  
  
The idea with these types is that they can be constructed from serialized UUID text, guaranteeing the format of the string to uphold the invariants.  
With this, they store/reference the UUID text, providing efficient access to the *validated* UUID text for subsequent logging or whatever. They essentially increase type-safety around the serialized format of UUIDs.

---

## Comment 1

> Username: pdimov  
> Created at: 2025-09-09 13:55:49 UTC  
> Url: https://github.com/boostorg/uuid/issues/175#issuecomment-3270863248  

I'm not sure that keeping the UUID in text representation is worth it. Validation takes about as much time as deserialization, so there's no gain there, the deserialized UUID is more compact, taking less memory which means less cache pressure, and serialization should be sufficiently fast so that the overhead on output is minimal.

---

## Comment 2

> Username: justend29  
> Created at: 2025-09-22 22:30:50 UTC  
> Url: https://github.com/boostorg/uuid/issues/175#issuecomment-3321704070  

Thanks for the feedback, @pdimov.  
  
The use case came from areas where the UUID is always in text format. Particularly, the UUID is read from the DB in text, it gets logged many times in text, and is then written to the DB in text. For this, serializing at every log message and DB interaction was a waste, but upholding the UUID type is important to distinguish it from an arbitrary string.  
  
I'll close the issue, and can always revisit if the use case is common. Thanks, again!
