# #569 - Document rationale for smaller design choices [Open]

> Username: vinniefalco  
> Created at: 2017-07-02 00:14:31 UTC  
> Updated at: 2018-12-21 15:08:50 UTC  
> Url: https://github.com/boostorg/beast/issues/569  

Which smaller design choices deserve an explicit rationale in the documentation?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-02 15:10:28 UTC  
> Url: https://github.com/boostorg/beast/issues/569#issuecomment-312497517  

Body concept design  
  
This can be its own section. Excerpts from the list:  
  
>`reader::get` and `writer::put` are part of the interfaces used to serialize and parse the body respectively. There can be state information associated with those operations which doesn't belong in the body and exists only during the parse or serialization. For example file_body stores FILE* during I/O. Using `Body body` instead of `Body::value_type body` would force every implementation of Body to store state information in the message. const messages could never be serialized or accessed from two threads simultaneously.  
  
>Another problem with requiring Body::get and Body::put is that it is intrusive. Existing containers could not be used as Body types due to the additional requirements. response<std::string> wouldn't work. This imposes an unnecessary burden on anyone who wants to use a particular container: a new class must be written which wraps the container and its interfaces, then adds the necessary get() and put() members. `string_body body` would have to duplicate every member function of std::string.  
  
>The current implementation is flexible and expressive. Any container may be used as the type for the message::body member. Users can add the required reader and writer implementations non-intrusively. State required only during serialization or parsing is managed outside of the container. Messages may be serialized by multiple threads concurrently. The scheme Beast uses for selecting the message body type is one of the defining characteristics of the library, and to my knowledge is the first of its kind.
