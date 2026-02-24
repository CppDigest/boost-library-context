# #1103 - Rename `json::parser::write` to `read` [Open]

> Username: OleksandrKvl  
> Created at: 2025-09-04 15:24:55 UTC  
> Updated at: 2025-09-19 15:18:59 UTC  
> Url: https://github.com/boostorg/json/issues/1103  

Naming `a::b` usually implies "a does b", e.g. `std::vector::push_back` means "vector pushes back". By definition, parser never writes, it only reads data so this method should be named `json::parser::read/consume/etc`.  
  
`json::serializer::read` has the same problem, `obj.read(data, size)` means object reads data of given size, `serializer::read` actually *writes* some data into the given buffer. Don't know if there are other components with this problem.

---

## Comment 1

> Username: jeremy-coulon  
> Created at: 2025-09-04 16:09:05 UTC  
> Url: https://github.com/boostorg/json/issues/1103#issuecomment-3254444614  

Or `parse()`

---

## Comment 2

> Username: grisumbras  
> Created at: 2025-09-11 07:41:26 UTC  
> Url: https://github.com/boostorg/json/issues/1103#issuecomment-3278932676  

To be clear, I will only consider it, if this issue accumulates _a lot_ of support. In the ballpark of 100 people. Nevertheless, the issue is useful, because we have got complaints about this particular nomenclature. So, this issue will be the place where the logic behind the names is explained.

---

## Comment 3

> Username: grisumbras  
> Created at: 2025-09-11 08:05:12 UTC  
> Url: https://github.com/boostorg/json/issues/1103#issuecomment-3279053890  

Vinnie Falco on why he chose these names:  
  
> write takes a const buffer and read takes a mutable buffer just like asio so I would be reluctant to want to reverse that  
  
In Asio a user _reads_ from a socket into a mutable buffer and _writes_ from a constant buffer into a socket. In that mental model, a parser is a "device" to which the user writes characters, and eventually the device produces a json value. Similarly, a serializer is a "device" which produces characters, which the user reads into a buffer.  
  
There is a sort of inversion of who does the action in peoples' mind, though. `parser.write(input)` implies for most people that the parser is doing the writing, it is the actor. Whereas the original mental model treats it as the target of the action.  
  
Peter Dimov's comment:  
  
> it's perfectly logical (fread writes into the provided buffer) but still confusing  
  
Same logic as with sockets: `FILE` is a "device" that produces `unsigned char`s, which the user reads into a provided buffer. This API is arguably less confusing, because this is a free function, and the `FILE` argument is the last, so there's less implication that the file is what doing the reading.

---

## Comment 4

> Username: OleksandrKvl  
> Created at: 2025-09-11 09:24:49 UTC  
> Url: https://github.com/boostorg/json/issues/1103#issuecomment-3279454704  

I think "device" is not correct abstraction here. IMO device is appropriate for something mostly focused on I/O while parser/serializer is focused on transformation. It can be considered as I/O *physically* but *logically* it's different. `read/write` terms for those actions are simply over-generalization.  
  
When people see `object.write(data)`, they think "object writes data", that's how clean code works and it shouldn't require some mental model in order to understand it. Otherwise it actually becomes a "code".  
  
For free functions this problem is much less pronounced because `write(object, data)` reads as "write data into object", though I still believe `read/write` are wrong terms for this domain.

---

## Comment 5

> Username: grisumbras  
> Created at: 2025-09-11 12:05:53 UTC  
> Url: https://github.com/boostorg/json/issues/1103#issuecomment-3280260225  

On your first paragraph: your understanding of the word "device" is too restrictive. I have an electric shaver, it is a device. Arguably it doesn't have input or output, it just performs its function. On the other hand, conceptually a "transformation" is also taking an "input" and producing an "output" (in other words, I/O).  
  
Your second and third paragraphs are just repeats of what I already wrote above. Yes, when a person sees `a.op(b)` he usually reads that as `a does op with b`. Which is indeed different from how `parser::write` and `serializer::read` are meant to be taken. And yes, `op(a, b)` is different in this regard, people are more willing to read that as `do op with a and b`.

---

## Comment 6

> Username: kelbon  
> Created at: 2025-09-11 19:04:29 UTC  
> Url: https://github.com/boostorg/json/issues/1103#issuecomment-3282282924  

Its `feed`

---

## Comment 7

> Username: virtualmatador  
> Created at: 2025-09-18 21:14:33 UTC  
> Url: https://github.com/boostorg/json/issues/1103#issuecomment-3309748442  

I don't think swapping read and write will do any good. Both can be used interchangeably to confuse people. Some examples to show they are interchangeable:  
  
my_serializer.read_from(source_storage);  
my_serializer.write_from(source_storage);  
  
In each transform, there is a **read** from a **source** and a **write** to a **destination**. Either object or the parameter could be source or destination, so naming a transformation read or write could be equally confusing. The ones that you think are not confusing are those you've got used to. Like  
  
source_file.read(destination_memory);  
destination_file.write(source_memory);  
  
I recommend choosing operators that inherently carry direction, like `<<` and `>>`.

---

## Comment 8

> Username: virtualmatador  
> Created at: 2025-09-19 15:18:59 UTC  
> Url: https://github.com/boostorg/json/issues/1103#issuecomment-3312613514  

> Its `feed`  
  
Also confusing:  
  
dog.feed(dog_food);  
parent.feed(child);  
dest_file.feed(source_container);   
source_file.feed(dest_container);
