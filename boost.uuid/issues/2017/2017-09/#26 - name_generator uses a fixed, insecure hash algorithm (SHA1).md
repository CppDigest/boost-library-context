# #26 - name_generator uses a fixed, insecure hash algorithm (SHA1) [Closed]

> Username: jeking3  
> Created at: 2017-09-05 16:21:08 UTC  
> Updated at: 2018-11-09 21:55:44 UTC  
> Closed at: 2017-10-10 13:01:45 UTC  
> Url: https://github.com/boostorg/uuid/issues/26  

Earlier this year Google announced that they had produced the first SHA1 collision:  
  
https://security.googleblog.com/2017/02/announcing-first-sha1-collision.html  
  
The boost::uuid library maintains a SHA1 implementation today for purposes of the name_generator, which takes any arbitrary sized set of data and generates a UUID from it.  The library is hardcoded to use the SHA1 hash algorithm today.  
  
The proposed improvement here is to make the hash algorithm configurable.  A new HashingProvider concept would be introduced (or at least discussed) and/or a pure virtual base specifying the required methods an algorithm would need to implement (process_byte, process_bytes, get_digest); modify name_generator to be a template class where the hash algorithm is supplied as a template parameter, and checked to be a base of this virtual class at compile time such that a usable error message would occur.  
  
To maintain backwards compatibility a default template parameter leveraging the existing SHA1 hash algorithm would be introduced, however it would be deprecated, and eventually the default would be removed thus forcing all use of name_generator to specify the hash algorithm as a mechanism to gently move them away from the less secure SHA1 to something else.  
  
It may also be useful to move hashing to its own library where the HashingProvider concept could live, similar to how the UniformRandomNumberGenerator concept lives in boost::random.

---

## Comment 1

> Username: Lastique  
> Created at: 2017-09-07 08:42:01 UTC  
> Url: https://github.com/boostorg/uuid/issues/26#issuecomment-327732549  

1. The UUID generation from name is described in [RFC4122](https://tools.ietf.org/html/rfc4122#section-4.3), which specifies MD5 or SHA1. I'm not sure the current implementation fully follows the spec, but I think it should be used as the reference and the default.  
2. If you still want to make the hash algorithm configurable, I would prefer an implementation without virtual functions. They are not required if you follow the templated approach - just define the concept of the hashing function and use the template argument accordingly.  
3. In order to not break backward compatibility I would suggest implementing the configurable version of `name_generator` under a different name, like `basic_name_generator` or something. `name_generator` can be made a typedef to this new implementation.

---

## Comment 2

> Username: jeking3  
> Created at: 2017-09-07 16:51:09 UTC  
> Url: https://github.com/boostorg/uuid/issues/26#issuecomment-327858788  

@Lastique Thank you for your comments.    
  
On item 1, hashes, given SHA1 has recently been compromised, it would make sense to start planning for a replacement.  This actually got me thinking as to whether a new IETF document with all the published errata and the addition of another hashing algorithm (or two?) would be warranted.  
  
On item 2, I think it would be an interesting exercise to do what you suggest, and add MD5 as an option.  Obviously there will be little to no use of it, but it will complete the implementation with respect to the RFC.  I like your strategy in item 3 as well.

---

## Comment 3

> Username: mclow  
> Created at: 2017-09-07 17:34:51 UTC  
> Url: https://github.com/boostorg/uuid/issues/26#issuecomment-327870319  

I can ask an IETF co-worker of mine about future work on RFC4122 (or a followup RFC).  
  
As a design comment, I agree with @Lastique that having a "hash function" as a template argument rather than using inheritance is a better way to go.  [ See the unordered containers for examples. ]

---

## Comment 4

> Username: jeking3  
> Created at: 2017-09-07 17:48:52 UTC  
> Url: https://github.com/boostorg/uuid/issues/26#issuecomment-327873994  

@mclow much appreciated - thank you for taking the time do check on that.

---

## Comment 5

> Username: jeking3  
> Created at: 2017-09-22 16:48:02 UTC  
> Url: https://github.com/boostorg/uuid/issues/26#issuecomment-331500076  

@mclow were you able to make any progress on a RFC4122 follow-up discussion?

---

## Comment 6

> Username: mclow  
> Created at: 2017-09-22 17:16:39 UTC  
> Url: https://github.com/boostorg/uuid/issues/26#issuecomment-331507119  

I believe that the IETF people are discussing it - but no resolution AFAIK yet.

---

## Comment 7

> Username: jeking3  
> Created at: 2017-09-25 23:03:11 UTC  
> Url: https://github.com/boostorg/uuid/issues/26#issuecomment-332038316  

@mclow I reached out by email to the original authors in RFC 4122 however paulle and rsalz emails bounced.

---

## Comment 8

> Username: jeking3  
> Created at: 2018-11-09 21:55:44 UTC  
> Url: https://github.com/boostorg/uuid/issues/26#issuecomment-437507270  

@mclow my emails to folks on the RFC 4122 bounced.  This issue has stalled.  Do you have any more contacts or should I think about starting an update of the RFC?
