# #578 - char8_t [Closed]

> Username: igoloe  
> Created at: 2021-06-06 10:22:29 UTC  
> Updated at: 2024-10-09 18:43:46 UTC  
> Closed at: 2024-10-09 15:50:59 UTC  
> Url: https://github.com/boostorg/json/issues/578  

Did you think of making the complete way strings are stored or used configurable to the underlying char type?  
  
At least char8_t would be a pleaure to avoid mojibake on Windows Platforms with C++20.  
  
Ofc, as argued before, this can be seen as a complete different json library, like wide boost.serialization.

---

## Comment 1

> Username: grisumbras  
> Created at: 2021-06-18 11:40:33 UTC  
> Url: https://github.com/boostorg/json/issues/578#issuecomment-863976236  

One of the core decisions of this library is that `json::string` is not a template and stores `char`s. This is unlikely to change in foreseeable future.

---

## Comment 2

> Username: igoloe  
> Created at: 2021-06-18 16:49:06 UTC  
> Url: https://github.com/boostorg/json/issues/578#issuecomment-864161871  

Agreed. It should not be a template, but the char type is configurable via a define, so you either build with char -- and have the luck not to be in windows or are able to enable utf8 soure charset option -- or are within a legacy or strong typed unicode domain and like to use char8_t as a complete unambigous representation.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-06-19 02:19:22 UTC  
> Updated at: 2021-06-19 02:19:37 UTC  
> Url: https://github.com/boostorg/json/issues/578#issuecomment-864342180  

I don't see what the problem is. According to wikipedia, "mojibake" is "the garbled text that is the result of text being decoded using an unintended character encoding." But Boost.JSON strings are unambiguously documented as valid, UTF-8 encoded strings, which `char` buffers are perfectly capable of storing. `char8_t` and `char` are not encodings, they are representations of individual characters which are not the same as code points.  
  
Perhaps you can give a very short piece of example code which demonstrates the problem you are encountering?

---

## Comment 4

> Username: ingo-loehken  
> Created at: 2021-07-19 10:07:34 UTC  
> Url: https://github.com/boostorg/json/issues/578#issuecomment-882421981  

The problem ist not about the json library.  
  
Its just a requirement that constantly pops up, if you work in huge legacy codebases and need to distinguish safely between unicode encoded an non unciode encoded strings.  
  
The common approach (by contract) to use std::string cannot ensure that a dev gets the encoding wrong. Having std::u8string solves this problem.  
  
To support this paradigm (std:::u8string rather than std::string to store utf8 encoded strings) seamless interoperability with other libraries (including boost.json) is of major interest.

---

## Comment 5

> Username: grisumbras  
> Created at: 2021-07-19 13:36:33 UTC  
> Url: https://github.com/boostorg/json/issues/578#issuecomment-882554194  

The way strings are stored internally is orthogonal to the library users' use of custom types to represent utf8-encoded string. Is using a function to convert between such a type and `json::string` an acceptable approach?

---

## Comment 6

> Username: ingo-loehken  
> Created at: 2021-07-28 09:07:57 UTC  
> Url: https://github.com/boostorg/json/issues/578#issuecomment-888145498  

Using a conversion function is trivial, but not a seamless an type safe enforcment for encoding.  
  
Thats the current use of `nlohmann::json` which often ends with an ANSI string not beeing encoded as utf8, before storing it into a json object. Such problems only appear at the point of deserialization, so its runtime and requires code to executed. The risc for such code to be deployed at customers is quite high.  
  
Thats the reason to ask for having a different way to express (we expect) utf8 (not by contract), but by type. To support mixed encoding systems, storing `ANSI` in `std::string` and utf8 in `std::u8string` to avoid misinterpretation of encoding and reduce riscs.

---

## Comment 7

> Username: grisumbras  
> Created at: 2024-10-09 15:50:59 UTC  
> Url: https://github.com/boostorg/json/issues/578#issuecomment-2402708055  

As mentioned previously `json::string` will not become a template, and will not provide a configuration macro to store some other type of characters. I'm closing this.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2024-10-09 18:17:36 UTC  
> Url: https://github.com/boostorg/json/issues/578#issuecomment-2402983403  

I agree that putting more type safety into the string will help address that one use-case. However this comes at the expense of the other use-case. I value `json::string` being a closed type more than helping authors of legacy code bases avoid mistakes.

---

## Comment 9

> Username: igoloe  
> Created at: 2024-10-09 18:28:56 UTC  
> Url: https://github.com/boostorg/json/issues/578#issuecomment-2403005010  

I am fine with that. We lost more than a decade with Unicode in C++, because we cannot commit to a common perspective. Its just a sad thing.  
  
Besides I did not recommend a template solution. The idea was more like the serialization lib in boost, delivering different binaries ... there are different ways in achieving that.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2024-10-09 18:43:45 UTC  
> Url: https://github.com/boostorg/json/issues/578#issuecomment-2403055210  

I don't like having different "flavors" of library because this just creates incompatibility and link-time headaches.
