# #841 - Confusing terminology in the docs [Closed]

> Username: JankoDedic  
> Created at: 2023-01-16 19:44:56 UTC  
> Updated at: 2023-04-02 22:20:46 UTC  
> Closed at: 2023-04-02 18:44:45 UTC  
> Url: https://github.com/boostorg/json/issues/841  

I've noticed that in some places like [here](https://www.boost.org/doc/libs/1_78_0/libs/json/doc/html/json/input_output.html#json.input_output.parsing), the term "a JSON" is used, for example:  
  
>In the following example **a JSON** is parsed from standard input a line at a time.  
  
Does this mean "a JSON value" or "a JSON text"? The latter is used in some places in the docs. Is this something different? I don't see the term "a JSON" defined in the [RFC](https://www.rfc-editor.org/rfc/rfc8259).  
  
May I request clarification?  
Thanks

---

## Comment 1

> Username: cdesouza-chromium  
> Created at: 2023-01-16 19:48:17 UTC  
> Url: https://github.com/boostorg/json/issues/841#issuecomment-1384484537  

Yeah, the ecma standard also seems to agree with the description of this issue.  
  
https://262.ecma-international.org/13.0/#sec-json.parse  
  
> 25.5.1 JSON.parse ( text [ , reviver ] )  
The parse function parses a JSON text (a JSON-formatted String) and produces an ECMAScript language value. The JSON format represents literals, arrays, and objects with a syntax similar to the syntax for ECMAScript literals, Array Initializers, and Object Initializers. After parsing, JSON objects are realized as ECMAScript objects. JSON arrays are realized as ECMAScript Array instances. JSON strings, numbers, booleans, and null are realized as ECMAScript Strings, Numbers, Booleans, and null.  
  
Seems weird that most of the documentation in this lib has chosen this unusual wording, that goes against any other web standard.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2023-01-16 20:47:50 UTC  
> Url: https://github.com/boostorg/json/issues/841#issuecomment-1384535717  

Current wording:  
  
> Parsing is the process where a serialized JSON is validated and decomposed into elements  
  
Should we say "JSON text?" What new wording are you proposing?

---

## Comment 3

> Username: JankoDedic  
> Created at: 2023-01-16 20:51:11 UTC  
> Url: https://github.com/boostorg/json/issues/841#issuecomment-1384537954  

Yeah, I think "JSON text" would be right.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2023-01-16 21:01:15 UTC  
> Url: https://github.com/boostorg/json/issues/841#issuecomment-1384544003  

Do you want to submit a pull request to fix it all?

---

## Comment 5

> Username: cdesouza-chromium  
> Created at: 2023-01-16 21:44:19 UTC  
> Url: https://github.com/boostorg/json/issues/841#issuecomment-1384593707  

I don't think I would be able to, unfortunately.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2023-01-16 22:35:01 UTC  
> Url: https://github.com/boostorg/json/issues/841#issuecomment-1384634457  

Who is using this library?

---

## Comment 7

> Username: cdesouza-chromium  
> Created at: 2023-01-16 23:46:42 UTC  
> Url: https://github.com/boostorg/json/issues/841#issuecomment-1384676617  

I came across that wording as I was studying the library to use it.

---

## Comment 8

> Username: cdesouza-chromium  
> Created at: 2023-04-02 22:14:01 UTC  
> Updated at: 2023-04-02 22:20:46 UTC  
> Url: https://github.com/boostorg/json/issues/841#issuecomment-1493452562  

Thanks for fixing this. Deleted previous comment, as I had misunderstood the fix for a second.
