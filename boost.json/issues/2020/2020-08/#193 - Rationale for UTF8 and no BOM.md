# #193 - Rationale for UTF8 and no BOM [Closed]

> Username: vinniefalco  
> Created at: 2020-08-23 19:16:14 UTC  
> Updated at: 2023-04-02 18:44:42 UTC  
> Closed at: 2023-04-02 18:44:42 UTC  
> Url: https://github.com/boostorg/json/issues/193  

> Boost.JSON does not support byte order marks. The parser will generate an error if it sees one.  
> in other words, if the caller cares about byte order marks, it is up to them to 1. detect them, and 2. decide what to do with it  
> Boost.JSON assumes that the JSON text is not part of a closed ecosystem (8.1)  
> Users who want to use it in a closed ecosystem, can add their own functionality around it  
> For example they can transcode utf32 or utf16 to utf8  
> They can inspect for a byte order mark and deal with it the way they want  
> if I add support to allow a byte order mark to appear at the beginning of parsed JSON, then I would have to return an error if that byte order mark did not specify UTF-8  
> we could at some point transcode non utf-8 input. but the resulting strings and keys would be utf-8. The library will never support different encodings for the container types, that would be insane  
> That is why I decided to just stick with utf8

---

## Comment 1

> Username: JonKalb  
> Created at: 2020-08-23 19:25:37 UTC  
> Url: https://github.com/boostorg/json/issues/193#issuecomment-678814225  

I think it makes sense to have an options that allows the user to specify that the BOM (if present) be either (a) silently ignored in all cases or (b) validated and ignored if it is a valid UTF-8 BOM.  
  
The user could, of course, just write code to advance the parse point past the BOM, but the point of a library is for code to be written once, not by every user that requires it.

---

## Comment 2

> Username: grisumbras  
> Created at: 2023-03-02 13:33:41 UTC  
> Url: https://github.com/boostorg/json/issues/193#issuecomment-1451873411  

RFC says that BOM MUST NOT appear in JSON text. It does allow implementations to ignore BOM (not replace them with zero width spaces as Unicode FAQ recommends). So, considering BOM a syntax error is a correct choice. Also, nobody asked for BOM support, thus I assume it's not really needed in the real world.  
  
Where would you recommend putting the note about only supporting UTF-8 without BOM? Overview?
