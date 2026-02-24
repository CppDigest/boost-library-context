# #11 - Invalid utf-8 strings are invalidly parsed as valid json [Closed]

> Username: Julien-Blanc-tgcm  
> Created at: 2019-11-25 05:39:08 UTC  
> Updated at: 2020-08-03 16:00:25 UTC  
> Closed at: 2020-08-03 15:59:41 UTC  
> Url: https://github.com/boostorg/json/issues/11  

See attached file. It should not be parsed correctly.  
  
[invalid_json.json.gz](https://github.com/vinniefalco/json/files/3884966/invalid_json.json.gz)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-25 14:17:51 UTC  
> Url: https://github.com/boostorg/json/issues/11#issuecomment-558175335  

Yes, the parser does not perform utf8 validation. I'm not sure if it should. The place to do it is here:  
https://github.com/vinniefalco/json/blob/develop/include/boost/json/impl/basic_parser.ipp#L497

---

## Comment 2

> Username: WPMGPRoSToTeMa  
> Created at: 2020-05-30 00:07:35 UTC  
> Updated at: 2020-05-30 00:08:39 UTC  
> Url: https://github.com/boostorg/json/issues/11#issuecomment-636242652  

[The JSON Data Interchange Syntax](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-404.pdf) says (emphasis mine):  
> A string is a **_sequence of Unicode code points_** wrapped with quotation marks (U+0022).  
  
So I think it should be (or must be?) validated since invalid UTF-8 is just a sequence of **UTF-8** code **units**, but not also a sequence of **Unicode** code **points**.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-05-30 03:23:58 UTC  
> Url: https://github.com/boostorg/json/issues/11#issuecomment-636268454  

To do this we need to  
  
* modify `detail::count_unescaped` to stop when it encounters a character with the high-bit set.  
  
* check if `(c & 0x80) != 0` here: https://github.com/CPPAlliance/json/blob/fcdf8856512b51109ec4fe6792a40302bb30768c/include/boost/json/basic_parser.hpp#L768  
  
* then, if we have at least 3 more characters, implement "fast" utf-8 validation (all bytes present), otherwise do a `goto` into a new state where we handle one character at a time and check for the end of the stream.  
  
This looks doable, but it will add an additional branch. We could put it after all the other checks for escapes, and then it would be zero cost. However, users will then complain that they want the parser to accept invalid UTF-8....

---

## Comment 4

> Username: WPMGPRoSToTeMa  
> Created at: 2020-05-30 04:08:35 UTC  
> Url: https://github.com/boostorg/json/issues/11#issuecomment-636272240  

> However, users will then complain that they want the parser to accept invalid UTF-8....  
  
Similar to accepting comments in JSON.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-05-30 14:24:28 UTC  
> Url: https://github.com/boostorg/json/issues/11#issuecomment-636337957  

> Similar to accepting comments in JSON.  
  
Yes and then if we parse and ignore comments, users will 1. want a way to turn it off, and 2. ask to have the comments preserved when serializing.

---

## Comment 6

> Username: WPMGPRoSToTeMa  
> Created at: 2020-05-30 23:13:10 UTC  
> Updated at: 2020-05-30 23:14:34 UTC  
> Url: https://github.com/boostorg/json/issues/11#issuecomment-636396713  

Probably it would be better to add a precondition (so we just expect, but don't validate) that input string should be valid utf-8 (and same for `json::string`).

---

## Comment 7

> Username: vinniefalco  
> Created at: 2020-05-30 23:23:41 UTC  
> Url: https://github.com/boostorg/json/issues/11#issuecomment-636397517  

Well, we can expect all we want but if the JSON comes from an untrusted network source that won't do much good. As for `json::string`, yes the caller is responsible for putting only valid UTF-8 in the string.

---

## Comment 8

> Username: WPMGPRoSToTeMa  
> Created at: 2020-05-30 23:45:30 UTC  
> Updated at: 2020-05-30 23:52:53 UTC  
> Url: https://github.com/boostorg/json/issues/11#issuecomment-636399070  

Yep, in this case `parse` should accept something like `valid_utf8_string_view` instead :smile: (or just an overload on that input type). And well, that overload can be added in future when there will be a standard way to pre-validate utf-8 strings.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2020-05-31 02:38:09 UTC  
> Url: https://github.com/boostorg/json/issues/11#issuecomment-636411931  

Well I think in this case we can afford to always do the validation, and provide a run-time switch to turn it off, without affecting performance.

---

## Comment 10

> Username: sdkrystian  
> Created at: 2020-08-03 15:59:41 UTC  
> Updated at: 2020-08-03 16:00:25 UTC  
> Url: https://github.com/boostorg/json/issues/11#issuecomment-668103041  

Implemented in d98b565887834bca73fde2cc8f0216de6c7992f8. Validation is enabled by default, but can be disabled by passing a `parse_options` object with `allow_invalid_utf8` set to `true` to `parser::parser`.
