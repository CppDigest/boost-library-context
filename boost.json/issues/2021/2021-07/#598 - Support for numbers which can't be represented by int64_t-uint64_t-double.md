# #598 - Support for numbers which can't be represented by int64_t/uint64_t/double [Closed]

> Username: yuri-kilochek  
> Created at: 2021-07-30 19:15:24 UTC  
> Updated at: 2021-08-03 03:31:09 UTC  
> Closed at: 2021-08-03 03:30:56 UTC  
> Url: https://github.com/boostorg/json/issues/598  

I want to interface with a finance service that unfortunately encodes monetary quantities as JSON numbers. To process those correctly, parsing and storage of numbers needs to be customized. Specifically, I want to parse them into `boost::multiprecision::cpp_dec_float`.  
  
(I realize that this particular case can be worked around by converting the parsed `double`s back to shortest string representation and parsing decimal floats from that. However, my point stands - it's useful to be able to handle numbers that can't be represented by `int64_t`/`uint64_t`/`double`, like big integers or arbitrary precision floats.)  
  
Well, I thought, I just need a template-based JSON library which would let me supply my custom numeric type and a parser for it. Something like `nlohmann::json`, perhaps. Except `nlohmann::json` doesn't let you supply arbitrary numeric types, only built-in ones. In fact, after a few hours spent surveying numerous C++ JSON libs on github, to my great surprise I couldn't find one that would let me do that.  
  
Of course, I realize `boost::json` is explicitly designed with different goals in mind. And I'm not advocating converting it to templates or anything like that. Fortunately, that's unnecessary. Consider:  
  
- To `parse_options` add `bool numbers_as_strings = false;` member (perhaps partitioned into `ints_as_strings`/`floats_as_strings`).  
- Find a single spare bit somewhere within `string` representation. Use it to indicate that this string was parsed from JSON number/must be serialized as JSON number. Let's call it `raw_number` flag. Initialize it to `false`. Add a getter and a setter for it, both public.  
- When parsing, if `numbers_as_strings` is set upon encountering a number, create a `string` consisting of exactly the characters comprising the number in input. Set the `raw_number` flag on it. Otherwise, parse `int64_t`/`uint64_t`/`double` as usual.  
- When serializing a `string`, if `raw_number` flag is set on it, assume the content is a valid JSON number and dump it into the output without quoting/escaping. Otherwise, serialize the string as usual.  
- Optionally, provide functions to convert `string` to/from `int64_t`/`uint64_t`/`double` using the algorithms currently employed by the parser/serializer.  
  
This moves number parsing/serialization to user code when necessary, in a way that doesn't complicate usage for people who don't care about this—admittedly quite rare—case. I believe it also fits the design principles of this library as I understand them. You guys have done a great job making common things easy, here I aim to make uncommon things possible.  
  
Do you consider this a reasonable design? Something you're willing to merge if presented with a PR? (not that I have one at the moment)

---

## Comment 1

> Username: WPMGPRoSToTeMa  
> Created at: 2021-07-30 19:49:18 UTC  
> Url: https://github.com/boostorg/json/issues/598#issuecomment-890117549  

Related: #101

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-07-30 22:42:22 UTC  
> Url: https://github.com/boostorg/json/issues/598#issuecomment-890223232  

I love that you have done the research to propose a possible implementation. You are right that the library is aimed towards a different goal. However if we can do this in a way that doesn't impact performance or usability, it could be worth exploring. @grisumbras do we have a spare bit in `json::string`?

---

## Comment 3

> Username: yuri-kilochek  
> Created at: 2021-08-01 14:25:42 UTC  
> Updated at: 2021-08-01 14:25:54 UTC  
> Url: https://github.com/boostorg/json/issues/598#issuecomment-890530270  

> do we have a spare bit in `json::string`?  
  
Bits 3, 4 and 5 within `kind` appear to be unused.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-08-01 14:52:18 UTC  
> Url: https://github.com/boostorg/json/issues/598#issuecomment-890533892  

hmm if we change `kind` then we incur extra instructions to decode the `kind`

---

## Comment 5

> Username: grisumbras  
> Created at: 2021-08-01 15:09:20 UTC  
> Url: https://github.com/boostorg/json/issues/598#issuecomment-890536871  

As @WPMGPRoSToTeMa have noticed, using a custom handler with `basic_parser` should allow this functionality already. Of course, writing a handler that differs from default one in just one aspect is a bit tedious. Maybe we can make the default handler public and/or provide an example of customizing it. We could use this use case for the example.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2021-08-01 15:28:28 UTC  
> Url: https://github.com/boostorg/json/issues/598#issuecomment-890539807  

Simple things are designed to be simple. Complex things will require more effort. We don't need to make authoring handlers less tedious. That said, the default handler is visible in the source code. And it is pretty darn simple :)  
  
https://github.com/boostorg/json/blob/develop/include/boost/json/detail/impl/handler.ipp

---

## Comment 7

> Username: yuri-kilochek  
> Created at: 2021-08-01 16:13:10 UTC  
> Updated at: 2021-08-01 16:14:40 UTC  
> Url: https://github.com/boostorg/json/issues/598#issuecomment-890546909  

> if we change kind then we incur extra instructions to decode the kind  
  
High bits of `kind` are already being used to indicate SBO and being-object-key, so the masking (that's what you mean by extra decoding instructions, right?) must already be happening.  
  
> use custom `basic_parser` handler  
  
I don't mind writing a custom handler that forwards most calls to `value_stack`, but that's not enough, is it? The `raw_number` flag still needs to be stored somewhere. `value_stack` doesn't expose the nodes being built, so I can't even hack together some kind of `std::unordered_set<boost::json::string*>` to keep track of strings that are actually numbers.  
  
Writing custom `string`, `value` and `value_stack` to make it work is a bit too much.  
  
And what about serialization?

---

## Comment 8

> Username: grisumbras  
> Created at: 2021-08-01 16:17:18 UTC  
> Url: https://github.com/boostorg/json/issues/598#issuecomment-890547820  

Depending on your final goal, you don't need `raw_number` flag. You'd have to write custom serialization code, though. Potentially, you could avoid `value` entirely and simply use `basic_parser` to build your own structure.

---

## Comment 9

> Username: yuri-kilochek  
> Created at: 2021-08-01 16:37:31 UTC  
> Updated at: 2021-08-01 16:45:14 UTC  
> Url: https://github.com/boostorg/json/issues/598#issuecomment-890551127  

> Depending on your final goal, you don't need `raw_number` flag.  
  
On the other hand, depending on your final goal, you _do_ need `raw_number` flag. So in general it should be possible to have it or something equivalent to it.  
  
> You'd have to write custom serialization code, though. Potentially, you could avoid value entirely and simply use basic_parser to build your own structure.  
  
Well, yes, I _could_ do all that. I could also write yet another JSON lib that would exactly satisfy all of my requirements. But that's a lot of work of dubious value I'd rather not do, hence why I'm here proposing a relatively simple improvement instead.

---

## Comment 10

> Username: grisumbras  
> Created at: 2021-08-01 16:53:57 UTC  
> Updated at: 2021-08-01 17:02:46 UTC  
> Url: https://github.com/boostorg/json/issues/598#issuecomment-890553065  

I'm curious, how do you envision the eventual usage? I mean, the whole parse-use-serialize cycle.

---

## Comment 11

> Username: yuri-kilochek  
> Created at: 2021-08-01 19:10:20 UTC  
> Updated at: 2021-08-01 20:20:53 UTC  
> Url: https://github.com/boostorg/json/issues/598#issuecomment-890571074  

I'm not sure I understand what you're asking. The eventual usage is exactly the same as it is right now, but additionally supporting large/precise numbers. Do you want an example? Here:  
  
```cpp  
void tag_invoke(boost::json::value_to_tag<boost::multiprecision::cpp_dec_float_50>,  
                boost::json::value const& json_value)  
-> boost::multiprecision::cpp_dec_float_50  
{  
    boost::json::string const& json_string = json_value.as_string();  
    if (!json_string.is_raw_number()) {  
        throw std::invalid_argument("not a number");  
    }  
    return boost::multiprecision::cpp_dec_float_50(json_string);  
}  
  
void tag_invoke(boost::json::value_from_tag,  
                boost::json::value& json_value,  
                boost::multiprecision::cpp_dec_float_50 const& value)  
{  
    boost::json::string& json_string = json_value.emplace_string();  
    json_string = value.str();  
    json_string.set_is_raw_number(true);  
}  
  
struct Order {  
    std::string item_id;  
    std::uint64_t amount;  
    boost::multiprecision::cpp_dec_float_50 price;  
};  
  
auto tag_invoke(boost::json::value_to_tag<Order>, boost::json::value const& json_value)  
-> Order  
{  
    boost::json::object const& json_object = json_value.as_object();  
    return {  
        .item_id = json_object.at("item_id").as_string(),  
        // assuming to_number is extended to support raw number strings  
        .amount = json_object.at("amount").to_number<std::uint64_t>(),   
        .price = boost::json::value_to<decltype(Order::price)>(json_object.at("price")),  
    };   
}  
  
void tag_invoke(boost::json::value_from_tag, boost::json::value& json_value, Order const& order) {  
    json_value = {  
        {"item_id", order.item_id},  
        {"amount", order.amount},  
        {"price", value_from(order.price)},  
    };  
}  
  
auto get_orders()  
-> std::vector<Order>  
{  
    std::string response_json = execute_get_orders_request();  
    boost::json::value json_value = boost::json::parse(response_json, {}, {  
        .numbers_as_strings = true,  
    });  
    return value_to<std::vector<Order>>(json_value);   
}  
  
void add_order(Order const& order)  
{  
    boost::json::value json_value = value_from(order);  
    std::string request_json = boost::json::serialize(json_value);  
    execute_add_order_request(request_json);   
}  
```

---

## Comment 12

> Username: vinniefalco  
> Created at: 2021-08-01 19:16:35 UTC  
> Updated at: 2021-08-01 19:16:58 UTC  
> Url: https://github.com/boostorg/json/issues/598#issuecomment-890571983  

Boost.JSON is a general purpose library which satisfies a specific, well-defined use case. It chooses 64-bit representations for integer and floating point numbers because that is what the vast majority of other libraries use (including JSON libraries in other languages such as Javascript and Python).  
  
The design of the library follows certain considerations:  
  
* No user-defined configuration  
* Variant sub-types are either built-in or defined by the library (no `std::string`)  
* All variant sub-types are first-class: equal capabilities  
* Variant sub-types are usable on their own (e.g. declare a `json::array`)  
* Non-use of features incurs no penalty (pay for what you use)  
  
There are three questions here:  
  
1. Should Boost.JSON support arbitrary precision numbers, and  
2. How would the library API represent these numbers?  
3. How does the parser and serializer account for these numbers?  
  
We will set question 1 aside for a moment and answer question 2. Given the previously mentioned design considerations, we would do the following:  
  
* Add a new `kind::bignum` to represent arbitrary precision numbers  
* Create `json::bignum` as a new type, with an optimized layout  
* Add the necessary APIs to `json::value`, e.g. `value::as_bignum`  
  
As for question 3, the parser must not impose a cost for users who don't care about bignums. If the parser must recognize that a string is actually a number, because it meets the syntactic requirements for a JSON number (minus the double quotes), the user must opt-in to this feature in a way that does not impact performance for the regular case. Most likely this would be a different parser.  
  
Back to question 1, I'm not entirely convinced that Boost.JSON should support arbitrary precision numbers. But even if it did, I don't think having a "flag" on strings is a good interface. It breaks the API design principles, because the user would be provided something resembling a number with no algorithms to operate on the number. Such as adding them together.  
  
General purpose libraries built on Boost.JSON would then have to know about a new variant sub-type and make provisions to deal with it.  
  
In the research stage of this library I realized that it is not possible to create a library that is both "complete" (i.e. satisfies most or all JSON use-cases) and also performant and concise. Adding features comes with a performance cost and also comes with an increased maintenance cost for general purpose libraries that want to operate on the `json::value` container, because they have to deal with a larger number of variant sub-types.  
  
I think for these reasons we should leave the library the way it is.

---

## Comment 13

> Username: yuri-kilochek  
> Created at: 2021-08-01 19:50:11 UTC  
> Updated at: 2021-08-01 20:16:42 UTC  
> Url: https://github.com/boostorg/json/issues/598#issuecomment-890576141  

> Back to question 1, I'm not entirely convinced that Boost.JSON should support arbitrary precision numbers. But even if it did, I don't think having a "flag" on strings is a good interface. It breaks the API design principles, because the user would be provided something resembling a number with no algorithms to operate on the number. Such as adding them together.  
  
This is false, the user will not be provided this unless he explicitly requests it via parse options. When it's not set, the behavior remains exactly as it it now and new APIs can be safely ignored. This also applies to libraries built on top of `boost::json`. I had assumed that full-blown bignums are out of scope of this library, hence this minimal proposal, which does not introduce a new variant subtype and does just enough to make it possible to interoperate with a specialized library like `boost::multiprecision`.  
  
> Given the previously mentioned design considerations, we would do the following:  
> -  Add a new kind::bignum to represent arbitrary precision numbers  
> - Create json::bignum as a new type, with an optimized layout  
> -  Add the necessary APIs to json::value, e.g. value::as_bignum  
>  
> As for question 3, the parser must not impose a cost for users who don't care about bignums. If the parser must recognize that a string is actually a number, because it meets the syntactic requirements for a JSON number (minus the double quotes), the user must opt-in to this feature in a way that does not impact performance for the regular case. Most likely this would be a different parser.  
  
Here you introduce a completely different design for this functionality and proceed to argue that it violates the design principles of this library. While I do agree, that's not what I'm proposing.  
  
> General purpose libraries built on Boost.JSON would then have to know about a new variant sub-type and make provisions to deal with it.  
> In the research stage of this library I realized that it is not possible to create a library that is both "complete" (i.e. satisfies most or all JSON use-cases) and also performant and concise. Adding features comes with a performance cost and also comes with an increased maintenance cost for general purpose libraries that want to operate on the json::value container, because they have to deal with a larger number of variant sub-types.  
  
Again, I'm not introducing a new variant sub-type here. Which of the library design considerations you mention does my proposal violate? You aren't seriously arguing that a 100% consistent branch on every number is going to have a noticeable performance impact when parsing/serializing?

---

## Comment 14

> Username: doganulus  
> Created at: 2021-08-02 10:42:00 UTC  
> Url: https://github.com/boostorg/json/issues/598#issuecomment-890922724  

I think `numbers as strings` is a hackish solution.   
  
A proper solution to the problem necessiates a new subtype. But that must be evaluated carefully as I don't want to pay for a performance cost becuase a financial service prints their big numbers without quotes somewhere.  
  
Overall I think it's easier to write a custom code to search and replace long digit sequences in text and add missing quotes before JSON parsing when consuming the service. And also I don't see why you would like to do the same mistake of that financial service in serialization.

---

## Comment 15

> Username: vinniefalco  
> Created at: 2021-08-03 02:53:33 UTC  
> Updated at: 2021-08-03 02:54:26 UTC  
> Url: https://github.com/boostorg/json/issues/598#issuecomment-891476182  

> ...hence this minimal proposal, which does not introduce a new variant subtype and does just enough to make it possible to interoperate with a specialized library like `boost::multiprecision`.  
> ...  
> Here you introduce a completely different design for this functionality  
  
The point is that **if** we are going to support arbitrary precision numbers, then it has to be done the same way as the other variant subtypes currently in the library. That is to say, we do **not** want "just enough to make it possible." Arbitrary precision numbers need to be treated equally well as every other subtype. Not as a flag placed on a `json::string` which the user has to perform additional operations on to obtain functionality.  
  
I appreciate that your proposal is minimally invasive but Boost.JSON is a critical, fundamental building block for many domains and thus every part of its API surface needs to have the same high level of quality.

---

## Comment 16

> Username: yuri-kilochek  
> Created at: 2021-08-03 03:30:56 UTC  
> Updated at: 2021-08-03 03:31:09 UTC  
> Url: https://github.com/boostorg/json/issues/598#issuecomment-891491694  

Fair enough. Thank you for your time.
