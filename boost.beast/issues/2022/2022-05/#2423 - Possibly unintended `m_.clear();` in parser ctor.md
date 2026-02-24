# #2423 - Possibly unintended `m_.clear();` in parser ctor? [Closed]

> Username: DisableAsync  
> Created at: 2022-05-07 16:14:26 UTC  
> Updated at: 2022-05-09 18:39:47 UTC  
> Closed at: 2022-05-09 18:39:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2423  

[Code of question](https://github.com/boostorg/beast/blob/90e37ae40bc80c99d1fae6078a6843a7e1d8f067/include/boost/beast/http/impl/parser.hpp#L36)  
  
I don't understand this ctor overload. We pass stuff in to construct a `http::message`, then we just clear it?  
Am I missing something or is this behavior unintended?

---

## Comment 1

> Username: sehe  
> Created at: 2022-05-07 16:25:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2423#issuecomment-1120236517  

The parser is used to create a new message from serialized form.  
  
The reason why a reference to a message instance can be passed is to allow re-use (reducing allocations).  
  
The clear makes sure that the output reflects the result of parsing.  
  
It is also assumed for internal logic (e.g. when protocol specifications are verified, lingering message state such as headers might trigger an error-condition or - worse - silently accept a malformed message).

---

## Comment 2

> Username: kcbsbo  
> Created at: 2022-05-07 17:08:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2423#issuecomment-1120242558  

well, but it's not reference type only

---

## Comment 3

> Username: sehe  
> Created at: 2022-05-07 20:08:34 UTC  
> Updated at: 2022-05-07 20:08:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2423#issuecomment-1120280168  

It's not a reference type. Is there a question?  
  
The parser takes ownership of the internal message resources (by move) and the caller can get it back by move via [`release()`](https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__http__parser/release.html).

---

## Comment 4

> Username: DisableAsync  
> Created at: 2022-05-08 04:30:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2423#issuecomment-1120348013  

> The reason why a reference to a message instance can be passed is to allow re-use (reducing allocations).  
>   
> The clear makes sure that the output reflects the result of parsing.  
  
Maybe we should update the code comments a bit?

---

## Comment 5

> Username: DisableAsync  
> Created at: 2022-05-08 04:38:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2423#issuecomment-1120348671  

This is just so confusing for me.  
  
The [comment of this ctor](https://github.com/boostorg/beast/blob/90e37ae40bc80c99d1fae6078a6843a7e1d8f067/include/boost/beast/http/parser.hpp#L98) says optional arguments are forwarded to message ctor.  Which is same for [this ctor](https://github.com/boostorg/beast/blob/90e37ae40bc80c99d1fae6078a6843a7e1d8f067/include/boost/beast/http/parser.hpp#L140) taking a rvalue reference parser as first parameter. But the latter does not clear message after construction.

---

## Comment 6

> Username: sehe  
> Created at: 2022-05-08 15:47:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2423#issuecomment-1120441821  

@DisableAsync The confusion is understandable.  
  
The second constructor is highly specific: it allows a parser to change its body type on the fly, explicitly retaining existing state. Note the extra requirements that are in place:  
  
> The constructed-from parser must not have any parsed body octets or initialized <em>BodyReader</em>, otherwise an exception is generated.  
  
This is explained in the documentation as "Deferred Body Type" in [Change Body Type 💡](https://www.boost.org/doc/libs/1_79_0/libs/beast/doc/html/beast/more_examples/change_body_type.html).  
  
In other words, the second constructor is a highly specific use-case assuming you Know What You're Doing. The first is necessarily more cautious to prevent issues like issue #818 (see also https://github.com/boostorg/beast/blob/develop/test/beast/http/parser.cpp#L332)

---

## Comment 7

> Username: DisableAsync  
> Created at: 2022-05-09 02:02:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2423#issuecomment-1120555477  

> The first is necessarily more cautious to prevent issues like issue #818 (see also https://github.com/boostorg/beast/blob/develop/test/beast/http/parser.cpp#L332)  
  
This issue looks more like a library API misuse (reusing the `request` object for different requests without clearing its states) to me.  
  
> The second constructor is highly specific: it allows a parser to change its body type on the fly, explicitly retaining existing state. Note the extra requirements that are in place:  
>> The constructed-from parser must not have any parsed body octets or initialized BodyReader, otherwise an exception is generated.  
  
I suppose that means the only state we actually reuse is the message header?  
  
If one can construct a new parser from an existing one, so that they get to use the constructed-from parser's header data., then why can't they just pass the header data itself to the parser for reuse?

---

## Comment 8

> Username: sehe  
> Created at: 2022-05-09 13:56:01 UTC  
> Updated at: 2022-05-09 13:56:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2423#issuecomment-1121135364  

> I suppose that means the only state we actually reuse is the message header?  
  
In that particular, well-documented, special use case, yes.  
  
> If one can construct a new parser from an existing one, so that they get to use the constructed-from parser's header data., then why can't they just pass the header data itself to the parser for reuse?  
  
You just observed that you already can. If the body type must change, use the OtherBody conversion constructor. If not, just continue with existing parser instance.  
  
If you can describe a valid use-case not supported by the existing functionality, please do so - perhaps in a separate ticket, instead of asking technical questions about individual lines of code. I don't mind answering those questions, but it's good to separate different questions.

---

## Comment 9

> Username: DisableAsync  
> Created at: 2022-05-09 18:15:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2423#issuecomment-1121423343  

> If you can describe a valid use-case not supported by the existing functionality, ...  
  
In that case, nope.   
  
Maybe I'm just overthinking too much. :P
