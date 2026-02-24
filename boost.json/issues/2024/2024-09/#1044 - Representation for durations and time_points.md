# #1044 - Representation for durations and time_points [Open]

> Username: sdebionne  
> Created at: 2024-09-19 13:52:09 UTC  
> Updated at: 2025-03-31 06:20:18 UTC  
> Url: https://github.com/boostorg/json/issues/1044  

About direct parsing, the documentation states that:  
  
> The drawback of this approach is that fully custom type representations are not supported, only the library-provided conversions are.  
  
I wonder where this limitation stands?  
  
I have a described struct with an `std::chrono::duration` member. While I have the proper tag_invoke overloads, since there is no built-in conversion support for std::chrono, I can't use `parse_into`. Is that correct?  
  
Would it be possible to add new customization points to allow direct parsing of custom types?  
  
Any chance to add a conversion category for date /time / duration (if that make sense)?

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-09-20 14:38:00 UTC  
> Url: https://github.com/boostorg/json/issues/1044#issuecomment-2363889167  

Direct parsing is implemented using a special event handler that is a tree of dedicated subhandlers for components of the target value, and works by descending to the first subhandlers that accepts a parsing event. The implementation relies on cooperation of parent and child subhandlers.  
  
In order to allow users to add support for their types we would have to make this system a public API and document how all that works. I'm not sure it's a very good idea, because writing those subhandlers is very tricky.  
  
I have an alternative idea, which I'm still not sure I could pull off, but we'll see. The idea is to have representation "proxies". You define a trait that your type is supposed to be represented as some other type, and converter/parser will create an object of that type and will do conversion/parsing into that object instead.  
  
This would provide a significant degree of customisation opportunities, like representing a chrono::duration as a number of ticks, or a chrono::time_point as an ISO 8601 string.

---

## Comment 2

> Username: pdimov  
> Created at: 2024-09-21 16:31:49 UTC  
> Url: https://github.com/boostorg/json/issues/1044#issuecomment-2365245708  

Support for `std::chrono::duration` should be built-in. `time_point` is maybe trickier. Or maybe not.

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-09-23 05:47:09 UTC  
> Url: https://github.com/boostorg/json/issues/1044#issuecomment-2367281872  

What should be the representation of `duration` in your opinion?

---

## Comment 4

> Username: sdebionne  
> Created at: 2024-09-23 07:10:08 UTC  
> Url: https://github.com/boostorg/json/issues/1044#issuecomment-2367388931  

I remembered you mentioned the idea of having "proxies" in your [latest blog post](https://cppalliance.org/dmitry/2024/07/12/dmitrys-q2-update.html) but to be honest I did not get it. What would be the proxy for `std::chrono::duration`, for instance?  
  
I support the idea of idea of having `std::chrono::duration` built-in. The representation could be either the number of ticks (int) and the period (int) to keep the full precision. Or the floating point representation in second.

---

## Comment 5

> Username: grisumbras  
> Created at: 2024-09-23 07:32:14 UTC  
> Url: https://github.com/boostorg/json/issues/1044#issuecomment-2367429645  

Representing them as a number of ticks has the drawback of all durations being represented in the same way (no way to tell 10 seconds and 10 minutes apart).  
  
Representing them as floating point value of seconds opens up the possibility of rounding errors.  
  
So, both approaches are not ideal.

---

## Comment 6

> Username: pdimov  
> Created at: 2024-09-23 08:28:02 UTC  
> Updated at: 2024-09-23 08:30:34 UTC  
> Url: https://github.com/boostorg/json/issues/1044#issuecomment-2367540419  

We have the following options (for e.g. a duration of 42 minutes):  
  
1. `42`  
2. `"42m"`  
3. `[42, 60, 1]`  
4. `[42, "m"]`  
5. `{"count": 42, "period": [60, 1]}`  
6. `{"count": 42, "period": "m"}`  
  
(1) has the disadvantage as stated above. Even though it works (for a struct member, for instance) because the period is encoded in the source code as the duration type, it would cause issues if two or more durations are put into a variant.  
  
(2) is unnecessarily hard to parse and kind of defeats the point of using a structured format such as JSON.  
  
(5) and (6) are the most human-readable, but overly verbose. For a file with many durations in it, they'll cause a lot of unnecessary overhead.  
  
(4) is more human-readable than (3), but harder to parse.  
  
This leaves me with (3). Interestingly though, we can _in principle_ make several of these work when parsing, because they can be disambiguated (but we do need to choose one when serializing.)  
  
So we can in theory add support for one of these, e.g. (3), today, and then later add support for e.g. (4) if we decide we like that better.  
  
Edit: there's also the option of `[42, [60, 1]]`, which is more regular, but there's no significant difference with (3) based on the criteria above, so that would be something like (3a) in our classification.

---

## Comment 7

> Username: pdimov  
> Created at: 2024-09-23 08:35:01 UTC  
> Url: https://github.com/boostorg/json/issues/1044#issuecomment-2367555179  

On second thought, I like `[42, [60, 1]]` better than `[42, 60, 1]` because it has more structure and doesn't match the representation of e.g. `std::vector<int>` or something like `math::point3<float>`.

---

## Comment 8

> Username: sdebionne  
> Created at: 2024-09-23 09:23:48 UTC  
> Url: https://github.com/boostorg/json/issues/1044#issuecomment-2367663725  

I would think that, in most applications, both side of the serialization agrees on the period, so the ticks representation would work.   
  
Is it not the responsability of the [schema to provide the unit/period](https://json-schema.org/understanding-json-schema/reference/string#dates-and-times)?

---

## Comment 9

> Username: grisumbras  
> Created at: 2024-09-23 09:47:30 UTC  
> Url: https://github.com/boostorg/json/issues/1044#issuecomment-2367721100  

As Peter mentioned, every ambiguous representation becomes a problem for variants. I.e. you have `variant<seconds, hours>`, you save `hours(10)`. You load `seconds(10)`. How common such cases would be is hard to say.  
  
> I would think that, in most applications, both side of the serialization agrees on the period, so the ticks representation would work.  
  
I would think, for JSON you usually don't control the format, which is why I prefer representations that don't just technically work, but are actually likely to be used by people. The spec you linked recommends ISO 8601 strings. It's actually not that hard to implement, so maybe we should use that.

---

## Comment 10

> Username: grisumbras  
> Created at: 2024-09-23 09:52:23 UTC  
> Url: https://github.com/boostorg/json/issues/1044#issuecomment-2367732615  

Although, maybe not. The linked ABNF doesn't allow fractions of seconds.

---

## Comment 11

> Username: sdebionne  
> Created at: 2024-09-23 11:53:14 UTC  
> Updated at: 2024-09-23 11:58:55 UTC  
> Url: https://github.com/boostorg/json/issues/1044#issuecomment-2367991267  

Indeed ISO 8601 is limited in precision to ms.   
  
Maybe it is not the responsibility of the JSON value itself to carry the unit/period. Instead this info could come from a schema. Something like:  
```  
uptime:  
  type: integer  
  unit: microseconds  
  example: 50  
```  
as discussed in https://github.com/json-schema-org/json-schema-vocabularies/issues/46.  
  
Taking [Boost.Units quantities](https://www.boost.org/doc/libs/1_86_0/doc/html/boost_units/Quantities.html) for example, we would probably represent it as numeric (or whatever the underlying value type matches best) and leave the unit to the schema?  
  
Side note: it is possible (tested to some extend) to generate a schema from a described struct.

---

## Comment 12

> Username: grisumbras  
> Created at: 2025-01-13 11:56:48 UTC  
> Url: https://github.com/boostorg/json/issues/1044#issuecomment-2586909535  

[CBOR](https://www.rfc-editor.org/rfc/rfc8949.html#name-standard-date-time-string) requires time points to be serialized according to `date-time` [in RFC3339](https://www.rfc-editor.org/rfc/rfc3339#section-5.6) with a restriction to use capital `T` and `Z`. There's also a [recent extension](https://www.rfc-editor.org/rfc/rfc9557#section-2) that suggests changing the semantics of `Z` to mean "offset from UTF to local time unknown", as opposed to using `-00:00` for that, because ISO 8601 does not allow `-00:00`.  
  
The timesatmps in RFC3339 aren't allowed to be in local time, interestingly.  
  
Also, I reopened the linked spec (JSON Schema) and the contents seem to have been dramatically changed, and now it also recommends RFC3339.  
  
Back to CBOR: it also has a dedicated type for epoch offset objects (what `chrono::time_point`s actually are). And those are represented as their representation types.  
  
So, it seems like there's no real default approach to timestamps. I am inclined to add several context objects for `time_point`s: one for representing as underlying number, one for representing as an ISO 8601-compatible string with TZ info, and one for such string, but without TZ info.  
  
And as I write this, I get yet another idea: represent `time_point` as a number always. But add a context that using the `represent_as` mechanic "maps" a `time_point` to a `zoned_time` (and back).

---

## Comment 13

> Username: grisumbras  
> Created at: 2025-03-31 06:20:17 UTC  
> Url: https://github.com/boostorg/json/issues/1044#issuecomment-2765247365  

Some data points. Yandex's userver has a JSON Schema-based IDL to generate JSON-based APIs. They use [plain numbers for durations](https://github.com/userver-framework/userver/blob/develop/chaotic/include/userver/chaotic/io/std/chrono/days.hpp#L13-L15), [RFC 3399 for dates (time points)](https://github.com/userver-framework/userver/blob/develop/universal/include/userver/utils/datetime.hpp#L21). They also have a special dat class that uses [a slightly different format](https://github.com/userver-framework/userver/blob/develop/universal/include/userver/utils/datetime.hpp#L30). Dates seem to always store a time zone. The time format difference is in how time zone offsets are represented.
