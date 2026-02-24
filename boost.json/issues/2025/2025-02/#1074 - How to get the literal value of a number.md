# #1074 - How to get the literal value of a number? [Open]

> Username: grapland0  
> Created at: 2025-02-24 07:12:46 UTC  
> Updated at: 2025-02-25 10:35:04 UTC  
> Url: https://github.com/boostorg/json/issues/1074  

For instance, for json content  
  
```json  
{"value": 1.23456789234567892345678923456789234567892345678923456789}  
```  
  
I'd like to get a string-like `1.23456789234567892345678923456789234567892345678923456789` from parsed `value`.  
  
This is useful not even for arbitrary precision floats, but for decimal floats. Number `0.1` has no accurate representation with IEEE754 binary float. Some users may want to pass this value as-is to their decimal float library, so they need the literal value.

---

## Comment 1

> Username: grisumbras  
> Created at: 2025-02-24 07:32:17 UTC  
> Url: https://github.com/boostorg/json/issues/1074#issuecomment-2677622492  

Currently you will have to use [`basic_parser`](https://www.boost.org/doc/libs/1_87_0/libs/json/doc/html/json/ref/boost__json__basic_parser.html) and write your own handler. The particular functions that deal with numbers are  
```c++  
bool on_number_part( string_view s, error_code& ec );  
bool on_int64( int64_t i, string_view s, error_code& ec );  
bool on_uint64( uint64_t u, string_view s, error_code& ec );  
bool on_double( double d, string_view s, error_code& ec );  
```  
  
The `string_view` argument there is the literal text from the input that is being parsed.

---

## Comment 2

> Username: grapland0  
> Created at: 2025-02-24 07:50:58 UTC  
> Url: https://github.com/boostorg/json/issues/1074#issuecomment-2677652407  

Thank you! Is it feasible to add a parser option to store all the numeric values as-is and provide API like `as_number_literal()" for access?

---

## Comment 3

> Username: grisumbras  
> Created at: 2025-02-24 07:55:06 UTC  
> Url: https://github.com/boostorg/json/issues/1074#issuecomment-2677659254  

Store strings instead of numbers? Okay, the question is how will you then know that a particular string is a number?

---

## Comment 4

> Username: RoyBellingan  
> Created at: 2025-02-24 08:13:32 UTC  
> Url: https://github.com/boostorg/json/issues/1074#issuecomment-2677691592  

to store something like  
{"value": 1.23456789234567892345678923456789234567892345678923456789}  
  
you will need 57 digit of precision which would require https://en.wikipedia.org/wiki/Octuple-precision_floating-point_format  
so a float256...  
  
I am inclined to think the number above came from something like an arbitrary precision library...  
So Is safe to assume is just better to handle them as string.

---

## Comment 5

> Username: grapland0  
> Created at: 2025-02-24 08:41:14 UTC  
> Updated at: 2025-02-24 08:42:32 UTC  
> Url: https://github.com/boostorg/json/issues/1074#issuecomment-2677743655  

For my specific use scenario, I just need decimal float, while I think they (with arb precision) fall in the same scope. Decimal float support is essential in financial and regulatory usage, while it is barely easy to make all API providers to go the preferred way to pass them as a string.  
  
Section 6 of RFC 8259 and section 8 of ECMA-404 (as well as ISO 21778) enforce the string format of a number in JSON. In RFC standard, they recommand, but not enforce the precision and limit. ECMA and ISO standard have no spec about this.   
  
I think it would be perfect for boost::json to have complete standard supported at common APIs.  
  
If we have number literal support, the basic verification based on the standards is sufficient, at the boost::json end.

---

## Comment 6

> Username: RoyBellingan  
> Created at: 2025-02-24 08:54:21 UTC  
> Url: https://github.com/boostorg/json/issues/1074#issuecomment-2677770554  

[DecimalFloat 128](https://en.wikipedia.org/wiki/Decimal128_floating-point_format) do not have enough digit of precision to store such long number, I am not aware if Decimal 256 even exists.  
  
From https://datatracker.ietf.org/doc/html/rfc8259#section-6  
> A JSON number such as 1E400  
   or 3.141592653589793238462643383279 may indicate potential  
   interoperability problems, since it suggests that the software that  
   created it expects receiving software to have greater capabilities  
   for numeric magnitude and precision than is widely available.  
  
I think it says do not expect such number to be supported, because float64 is what you reasonably will find to be used.

---

## Comment 7

> Username: grisumbras  
> Created at: 2025-02-24 08:54:44 UTC  
> Url: https://github.com/boostorg/json/issues/1074#issuecomment-2677771433  

> So Is safe to assume is just better to handle them as string.  
  
Handle them as strings how? You get a value that contains a string (`jv == "0E0"`). How do you determine that this not actually a string, but a number? Do you then attempt to parse it again?  
  
> ... they recommand, but not enforce the precision and limit.   
  
JSON spec does not mandate the maximum supported number precision out of necessity. It also doesn't mandate the maximum size of arrays, objects, and strings, because in practice there's always a size that wouldn't fit in computer's memory.  
  
Again, let's imagine the parser just created a string instead of a number. Now you have a value that is a string. How do you know that it is a number? If you use some out-of-band information for this (e.g. there is a fixed format), then this is a custom scenario. And I don't see a generic solution to this.  
  
Can you describe your scenario in more detail, so that I could look if a better solution than using `basic_parser` exists?

---

## Comment 8

> Username: grapland0  
> Created at: 2025-02-24 10:51:13 UTC  
> Updated at: 2025-02-24 10:56:35 UTC  
> Url: https://github.com/boostorg/json/issues/1074#issuecomment-2678053122  

My detailed scenario is like, I get 10 JSONs like `{"ask": 538.02, "bid": 538.01}` and I need to send a report in JSON to next service, with the accumulated value of all the "ask" fields from inputs. Here the expected result is `5380.2`, or in boost::json way, `5.3802E3`.  
  
But this test fails:  
```cpp  
TEST(utils_json, to_decimal)  
{  
  std::string input_sj = R"json({"ask": 538.02, "bid": 538.01})json";  
  auto opt = boost::json::parse_options{  
      .numbers = boost::json::number_precision::precise,  
  };  
  boost::json::value input_j = boost::json::parse(input_sj, {}, opt);  
  double accu = 0.;  
  for (int i = 0; i < 10; i++) {  
    accu += input_j.as_object().at("ask").as_double();  
  }  
  boost::json::value output_j = accu;  
  std::string output_sj = boost::json::serialize(output_j);  
  ASSERT_EQ(output_sj, "5.3802E3");  
}  
```  
```  
Expected equality of these values:  
  output_sj  
    Which is: "5.380200000000001E3"  
  "5.3802E3"  
```  
  
The root cause here is, `538.02` cannot be represented in IEEE754 binary float. The error is ampfied and output to `output_sj` and failed the test.  
  
The best way to resolve this problem is to provide raw access to number value. But as you said, we still need to type it as some kind of "number", so I'm proposing something like this (with Decimal as user defined decimal float number class):  
  
```cpp  
TEST(utils_json, to_decimal_2)  
{  
  std::string input_sj = R"json({"ask": 538.02, "bid": 538.01})json";  
  auto opt = boost::json::parse_options{  
      .numbers = boost::json::number_precision::as_literal,  
  };  
  boost::json::value input_j = boost::json::parse(input_sj, {}, opt);  
  Decimal accu("0");  
  for (int i = 0; i < 10; i++) {  
    std::string value = input_j.as_object().at("ask").as_number_literal();  
    accu += Decimal(value);  
  }  
  boost::json::value output_j =  
      boost::json::value_from(boost::json::number_literal_wrapper(accu.to_string()));  
  std::string output_sj = boost::json::serialize(output_j);  
  ASSERT_EQ(output_sj, "5.3802E3");  
}  
```

---

## Comment 9

> Username: RoyBellingan  
> Created at: 2025-02-24 12:36:13 UTC  
> Updated at: 2025-02-24 12:36:41 UTC  
> Url: https://github.com/boostorg/json/issues/1074#issuecomment-2678293679  

But that is intrinsic in how float works, and the same reason why should avoid to do  x == y for them. But you have to compare if number are "close enought" within an epsilon https://en.wikipedia.org/wiki/Machine_epsilon  
  
I once again suggest using string instead of floating point number.  
  
A practical case that cames to my mind is when working with google ads, they send all economic amount  as an integer, where they multiply the value by 1E6 to avoid problem with rounding or float conversion etc.  
  
So 10.45 will be 10450000

---

## Comment 10

> Username: grisumbras  
> Created at: 2025-02-24 12:59:56 UTC  
> Url: https://github.com/boostorg/json/issues/1074#issuecomment-2678350069  

So, here's an example of a program that uses a custom handler to populate the structure you described: https://godbolt.org/z/TbsbfzzTr.  
Essentially, the handler implents a state machine that fills the fields if the incoming data has the right structure.  
  
In a real program the handler would be a bit more complicated at the very least because you probably would want more specific error codes.  
  
I understand, that your real data probably has more complex structure. And writing a handler that deals with such data is significantly more complicated. We implement most of this functionality already in `parse_into`. I can see the argument, that we should provide a way for users to use big numbers and decimal numbers with `parse_into`. Is that something you'd be interested in?

---

## Comment 11

> Username: grapland0  
> Created at: 2025-02-24 15:29:29 UTC  
> Updated at: 2025-02-24 15:32:07 UTC  
> Url: https://github.com/boostorg/json/issues/1074#issuecomment-2678813436  

> I once again suggest using string instead of floating point number.  
  
It is not feasible for me as an API user of external web services. If I'm crawling data from SEC.gov and they give me a HTTP response with `580.02` as a JSON number, there is no way to persuade them to change the API just for me, especially when they are indeed providing the exact number in a way conforming to the ECMA and ISO standard of JSON.  
  
> I can see the argument, that we should provide a way for users to use big numbers and decimal numbers with parse_into. Is that something you'd be interested in?  
  
Structured import is a good thing, while can we have literal value access in unstructured way just as easy as value.as_double()?  
  
Furthermore, I think literal value access could be a first-class way to access JSON numbers, because:  
  
1. It provides a way to users to have full access on any reasonable JSON content with ISO/RFC/ECMA standards. Basically int64 and double are lossy representation of number values defined in standards.  
  
2. "don't pay for what you don't use". What if an user just wants a Single Precision number? If they have access to literal value, they can directly get the SP value as they need. Currently they have to down cast from the result of as_double(). The double precision ops involved are not only unnecessary, but also quite luxury in most hardware platforms, especially for embedded systems without hardware DP.  
  
3. If someday boost::json goes to the c++ standard, I guess all builtin numeric types must be supported, or there must be a reason why only int64/double are specifically selected to be supported.   
  
4. ECMA/ISO/RFC standards have no definition of "float". `1E3` and `1000` are identcial as number in JSON. So there looks like a unnecessary round trip to convert them to one kind of ints and doubles and then join together logically at user ends (with to_number). User should choose a representation of number at their end after the value gets populated from JSON content.

---

## Comment 12

> Username: RoyBellingan  
> Created at: 2025-02-24 16:53:29 UTC  
> Url: https://github.com/boostorg/json/issues/1074#issuecomment-2679077898  

If the objective is to pass the test, you might try to use also an example based on https://live.boost.org/doc/libs/1_87_0/libs/json/doc/html/json/examples.html   
  
And write in a format that is closer to the original json style ?

---

## Comment 13

> Username: grisumbras  
> Created at: 2025-02-25 00:45:52 UTC  
> Url: https://github.com/boostorg/json/issues/1074#issuecomment-2680055501  

> I think literal value access could be a first-class way to access JSON numbers  
  
What is literal access? What is stored in the `json::value` object?

---

## Comment 14

> Username: grapland0  
> Created at: 2025-02-25 01:22:57 UTC  
> Url: https://github.com/boostorg/json/issues/1074#issuecomment-2680121222  

> What is literal access? What is stored in the object?json::value  
  
literal value access is the access to the raw string of the number value in source JSON.  
  
It can reuse the facility of the current string value.

---

## Comment 15

> Username: grisumbras  
> Created at: 2025-02-25 01:25:48 UTC  
> Url: https://github.com/boostorg/json/issues/1074#issuecomment-2680124418  

Access from where? There's already access to it from handlers. After parsing is done, the "raw string of the number in source json" is gone. How would you access it then?

---

## Comment 16

> Username: grapland0  
> Created at: 2025-02-25 02:49:42 UTC  
> Url: https://github.com/boostorg/json/issues/1074#issuecomment-2680269289  

a new parsing options can be added as  
  
```cpp  
auto opt = boost::json::parse_options{  
    .numbers = boost::json::number_precision::as_literal,  
};  
```  
  
with this, instead parsing numbers to double or int64s, the raw string of numbers is persisted in json::value and can be accessed via as_literal_number().

---

## Comment 17

> Username: grisumbras  
> Created at: 2025-02-25 10:01:05 UTC  
> Url: https://github.com/boostorg/json/issues/1074#issuecomment-2681407466  

Ok, so you just want a way to make parser create a `json::string` instead of a number. I'm more inclined towards just making `detail::handler` public and providing an example along these lines: https://godbolt.org/z/a6sP5na64.  
  
Note, that this will not actually help you with serialization.  
  
Also, I see no point in adding any kind of `as_number` member to `json::string`, because it would be essentially equivalent to  
`double d; charconv::from_chars(js, d);`

---

## Comment 18

> Username: grapland0  
> Created at: 2025-02-25 10:34:01 UTC  
> Url: https://github.com/boostorg/json/issues/1074#issuecomment-2681505509  

> Ok, so you just want a way to make parser create a json::string instead of a number.  
  
This can resolve the problem for me. Serialization is OK for now as I can always find the nearest binary double to the short decimal value I have.  
  
The only concern is that the value type is lost, as they are now all json::string. It could be better to have something like `json::number_string` dedicated for raw string from number field. This also benefit serialization as it can just do plain copy instead of conversion from double/int.
