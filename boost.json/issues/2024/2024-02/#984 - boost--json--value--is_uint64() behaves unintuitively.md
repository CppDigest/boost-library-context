# #984 - boost::json::value::is_uint64() behaves unintuitively [Open]

> Username: DeaAlexandris  
> Created at: 2024-02-25 15:26:13 UTC  
> Updated at: 2025-11-21 14:14:35 UTC  
> Url: https://github.com/boostorg/json/issues/984  

Currently using Boost 1.83.0  
  
I'm developing a program with boost::json and want to be able to tell if an incoming numeric value is signed or not, and I find the behavior of boost::json::value::is_int64() boost::json::value::is_uint64() and boost::json::value::is_double() to behave in a way that is highly unintuitive.  
  
### Steps necessary to reproduce the problem  
Take the simple JSON:  
`{ MyVal: 2 }`  
  
2 is a small whole number and could easily be interpreted as int64, uint64, or double without trouble  
The JSON specification wisely does not demand that libraries adhere to any default type considerations when parsing and so it is left to the implementation to determine what primitive type the number is.  
  
if one were to parse the json to a value the following would all be true  
  
```  
// assume that MyVal is a boost::json::value type corresponding to MyVal above  
MyVal.is_int64() == true  
Myval.is_uint64() == false  
MyVal.is_double() == false  
```  
And indeed attempting to run the conversions would result in the following:  
```  
MyVal.as_int64(); // GOOD no except  
MyVal.as_uint64(); // exception  
MyVal.as_double(); // exception  
```  
This is inline with boost documentation, but it doesn't make sense to me, using only the information contained within the JSON it is impossible for boost::json to tell exactly what primitive type is actually a best fit for this value. If a developer is ingesting multiple JSONs with this structure as part of an API, and all the valid JSONs are unsigned whole numbers as I am, then when low numbers like this are received exceptions are possible, and the code must be written instead:  
```  
try  
{  
    unsigned long long int Output = MyVal.to_number<unsigned long long int>();  
}  
catch (...)  
{  
    // record some error, toss out the JSON, etc.  
}  
```  
  
I don't see any reason, logically, why for numbers like 2 the method value::is_uint64() should return false instead of true,  
or why the conversion value::as_uint64() should throw an exception, except to match the result of value::is_uint64()  
  
This problem appears to be compounded by the fact that the JSON specification does not allow for leading zeros which might otherwise indicate the full scope of a number like this one; notably it does appear to allow for trailing zeros and decimals which could assist with finding doubles.  
  
Would it not be better for ambiguous numbers like these to present true for all type checking methods where the type could accurately represent the value being checked?  
  
### All relevant compiler information  
using g++ 13.2.1

---

## Comment 1

> Username: pdimov  
> Created at: 2024-02-25 15:36:56 UTC  
> Url: https://github.com/boostorg/json/issues/984#issuecomment-1962977308  

This comes up periodically; the problem is that as_uint64 returns a reference to uint64_t, and when the active field is of type int64_t, even if the numeric value is unsigned, the object is not of the correct type.  
  
But I think it can be made to work reasonably well: https://github.com/boostorg/json/issues/932#issuecomment-1714200548

---

## Comment 2

> Username: pdimov  
> Created at: 2024-02-25 15:40:24 UTC  
> Url: https://github.com/boostorg/json/issues/984#issuecomment-1962978274  

as_double can't give you a reference to double if the stored number is int64/uint64, though.

---

## Comment 3

> Username: DeaAlexandris  
> Created at: 2024-02-25 15:43:44 UTC  
> Url: https://github.com/boostorg/json/issues/984#issuecomment-1962979172  

My immediate reaction:  
Apologies for not checking the closed issues first, I did check the open ones but I should have checked the closed as well.  
I think I understand what you're saying as well, since logically the type has to be assigned to something when it is initially parsed, and there's nothing we can assign it to which is "safe" for any numeric type.

---

## Comment 4

> Username: grisumbras  
> Created at: 2024-02-25 18:04:21 UTC  
> Url: https://github.com/boostorg/json/issues/984#issuecomment-1963015600  

I feel like this is one of those issues where you have one perspective and me, the maintainer, have a different perspective and we will eventually agree to disagree. But anyway...  
  
The JSON spec doesn't use "ints" or "uints" of any specific or non-specific size, neither it uses "doubles". It only refers to numbers. This is because the spec is based on JavaScript types, and the only numeric type there is Number. The spec also only touches upon implementation-related restrictions on numbers, specifically it doesn't recommend using numbers outside of range of IEEE 754 binary64 in JSON documents, because such numbers might not be supported by all implementations. This makes the spec rather irrelevant when discussing wether `is_int64` or `as_double` has unintuitive behaviour.  
  
Now, C++ is not JavaScript, it has different arithmetic types for different purposes. This library could have chosen to only store numbers as `double`s. But that would result in inability to store many large integer numbers precisely. Instead we try to store integers as `int64_t`, if that's impossible then as `uint64_t`, and if that is also impossible then as `double`.  
  
The member functions of `json::value` which in the familes `is_`, `as_`, and `get_` deal with the internal representation, and _not_ with potential semantic meaning. The functions aren't called `is_positive_integer` or `is_fractional_number` after all. This is more obvious with `as_` and `get_` functions, as they return references and you normally can't (or shouldn't) return a reference to something that doesn't exist. But the same logic is used for `is_` functions, because their main purpose is code like this:  
  
```c++  
value jv = ...;  
if( jv.is_int64() )  
{  
  foo( jv.get_int64() ); // guaranteed to work  
  foo( jv.as_int64() ); // guaranteed to not throw  
}  
```  
  
So, now let's try solving the original problem you've had. Given that you don't care which specific arithmetic type is stored inside the value, and you only need to know the sign, you can just convert the number to double:  
  
```c++  
boost::system::error_code ec;  
auto const d = jv.to_number<double>(ec);  
if( ec )  
  return false;  
return d >= 0;  
```

---

## Comment 5

> Username: DeaAlexandris  
> Created at: 2024-02-25 18:41:48 UTC  
> Url: https://github.com/boostorg/json/issues/984#issuecomment-1963025329  

Thanks everyone who has weighed in, and thanks to the maintainer specifically for offering a code suggestion. Some notes about my initial issue posting where I could've done better:  
    I definitely should have looked for closed issue matching this one  
    I should not have included doubles in my example.  
    and finally for my specific use case I am actually trying to identify not just positive numbers, but positive whole numbers  
  
After having found where the code is actually stored (Not in this repo?)  I have come to understand a bit more (albeit not completely) why this is the case.  
  
Surprisingly:  
  
> ...as they return references and you normally can't (or shouldn't) return a reference to something that doesn't exist.   
  
Is definitely inaccurate as the data in question is stored in a union and for the values in question (at least for int64 and uint64) are indistinguishable from each other. So it's not that the reference doesn't exist or that even that it would need to be converted, but rather the problem here seems to be the simple nature that kind() cannot possibly evaluate to 2 different types at once. A problem that- after having read through it, I'm perfectly fine with. Though it may be unintuitive it does in fact work, and in any case I'm sure any attempts to change a library like this one could negatively impact many users who are not myself.  
  
Ergo, I will close issue and deepen my understanding.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2024-02-25 18:55:04 UTC  
> Url: https://github.com/boostorg/json/issues/984#issuecomment-1963028658  

this is a bit of a rough spot but difficult to "fix" as no perfect solution exists. Would have been nice if `to_number<T>()` returned `result<T>` as then you can write `jv.to_number<double>().value()`

---

## Comment 7

> Username: jaques-sam-tlv  
> Created at: 2025-11-19 12:40:01 UTC  
> Url: https://github.com/boostorg/json/issues/984#issuecomment-3552496284  

I don't see why this was closed. "2" is a signed and an unsigned number, so both `as_uint64` and `as_int64` should work.  
It's wrong to throw when trying to parse with `as_uint64`!!

---

## Comment 8

> Username: vinniefalco  
> Created at: 2025-11-19 16:42:36 UTC  
> Url: https://github.com/boostorg/json/issues/984#issuecomment-3553690672  

Please read the documentation more carefully

---

## Comment 9

> Username: jaques-sam-tlv  
> Created at: 2025-11-20 07:28:20 UTC  
> Url: https://github.com/boostorg/json/issues/984#issuecomment-3556344050  

Okay, I found it:  
> More formally, if the number: contains a decimal point, or contains an exponent, or is negative and its value is less than INT64_MIN, or is positive and its value is greater than UINT64_MAX, then its type is double. Otherwise, if the number is positive and its value is greater than INT64_MAX, then its type is std::uint64_t. All other numbers are parsed as std::int64_t.  
  
I dislike the fact that an exception is thrown for something unclear and people have to look for the documentation.  
  
IMHO, the root cause of the problem lies in the parsing approach of having only 1 `json::kind` for a json value representation.

---

## Comment 10

> Username: grisumbras  
> Created at: 2025-11-20 08:20:26 UTC  
> Url: https://github.com/boostorg/json/issues/984#issuecomment-3556508680  

> "2" is a signed and an unsigned number  
  
No, something cannot be a "signed and unsigned number" in C++. Integral types are either signed or unsigned. Presumably, you wanted to say somethon like "there is no distinction between signed and unsigned numbers in JSON". This is true, but there is also no distinction between integral numbers and non-integral ones, it's just numbers. Should "2.0" produce a `value` that would give you 2 when `as_uint64()` is called on it? Should "2" produce a `value` that would give you 2.0 when `as_double()` is called on it   
  
> It's wrong to throw when trying to parse with as_uint64!!  
  
Here we see the main problem. `as_uint64` does not parse anything. You fundamentally did not undestand what that function does. The function exists for a specific reason. If it had the behaviour you presumably want (that is, convert the underlying value to `uint64_t`, not parse obviously), it and similar functions (`as_double`, `as_int64`) would have been unnecessary and would have been removed. The behaviour you ask for is already provided with `to_number` (as was mentioned in this discussion above: https://github.com/boostorg/json/issues/984#issuecomment-1963015600).  
  
> I dislike the fact that an exception is thrown for something unclear and people have to look for the documentation.  
  
The documentation exists to clarify things. If something is unclear, the docs is exactly where you should look for.  
  
> IMHO, the root cause of the problem lies in the parsing approach of having only 1 json::kind for a json value representation.  
  
Can you elaborate on that? Are you implying `value` should store several objects of different types simultaneously? So, `value(2)` would be akin to a tuple of `{ int64_t(2), uint64_t(2), double(2) }`?

---

## Comment 11

> Username: jaques-sam-tlv  
> Created at: 2025-11-20 10:29:37 UTC  
> Url: https://github.com/boostorg/json/issues/984#issuecomment-3557144946  

Thanks for the clarification. You're definitely right about topic 2,3 & 4. `to_number` is indeed a solution, having multiple solutions is sometimes confusing for the user (just a rant).  
Sorry if I wasn't clear, as I'm just a simple user of the boost json module and with the first topic, I already tried to make this the answer to your question. Indeed, for performance reasons you probably don't want to store these objects as such, but only allow a tuple of different kinds. Don't know if that's possible, otherwise forget what I'm saying...

---

## Comment 12

> Username: grisumbras  
> Created at: 2025-11-20 10:38:29 UTC  
> Url: https://github.com/boostorg/json/issues/984#issuecomment-3557196688  

I understand the issue with having functions that do similar things. Maybe it would have been better if those functions were called `as_underlying_X`, `get_underlying_X` and so on.

---

## Comment 13

> Username: pdimov  
> Created at: 2025-11-20 12:11:48 UTC  
> Url: https://github.com/boostorg/json/issues/984#issuecomment-3557697629  

As I already said the number of times, the present behavior is technically correct, but this will continue to serve as a trap for unsuspecting users and we can compromise and fix it.  
  
The problem is that the `as_X` functions return a reference to the underlying value, so if the value stored is of type `T`, they can give you a `T&`, but they can't give you a reference to something else. That's why the types `X` and `T` have to be an exact match.  
  
However, there's a special dispensation in the standard that allows manipulating a value of unsigned integer type via a reference to the corresponding signed integer type, and vice versa. This makes it possible for `as_uint64` to return a reference to the underlying `int64`, and for `as_int64` to be able to return a reference to an underlying `uint64`. So we can make this work, if we decide to.  
  
This is what will need to happen:  
  
* If `as_uint64` is called on a `value` storing `int64`, and the value inside is negative, throw as before.  
* If `as_int64` is called on a `value` storing `uint64`, and the value inside is bigger than `INT64_MAX`, throw as before.  
* Otherwise, if `as_uint64() const` is called on a `value` storing `int64`, return a reference to the value.  
* Otherwise, if `as_int64() const` is called on a `value` storing `uint64`, return a reference to the value.  
* Otherwise, if `as_uint64()` (non-const) is called on a `value` storing `int64`, change the kind to `uint64` and return a reference to the value.  
* Otherwise, if `as_int64()` (non-const) is called on a `value` storing `uint64`, change the kind to `int64` and return a reference to the value.  
  
In addition, we'll have to physically store both `int64` and `uint64` in the same `uint64` union field.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2025-11-20 23:25:36 UTC  
> Url: https://github.com/boostorg/json/issues/984#issuecomment-3560616835  

Wait... when you say "change the kind" do you mean actually changing the `json::value` discriminant? Or do you simply mean `reinterpret_cast` to compute the return value?

---

## Comment 15

> Username: pdimov  
> Created at: 2025-11-20 23:45:07 UTC  
> Url: https://github.com/boostorg/json/issues/984#issuecomment-3560669903  

Changing the discriminant, yes.

---

## Comment 16

> Username: vinniefalco  
> Created at: 2025-11-21 00:29:49 UTC  
> Url: https://github.com/boostorg/json/issues/984#issuecomment-3560775069  

`sizeof(json::value)` should remain unchanged if I understand this proposal correctly. I like it !

---

## Comment 17

> Username: grisumbras  
> Created at: 2025-11-21 06:48:20 UTC  
> Url: https://github.com/boostorg/json/issues/984#issuecomment-3561661111  

I don't like this idea. First of all, I think the problem is not that serious and is simply related to somewhat ambiguous naming.   
  
Secondly, there is a certain parallelism between `value`'s accessor functions. If `as_int64` and `as_uint64` are changed, then the corresponding `if_`, `is_`, `get_`, and `try_as_` functions would have to change too. This would be particularly unfortunate for the `get_` functions, as we'd have to add checks into them. And the entire sets of `_int64` and `_uint64` functions become a special case that will surprise people in its own right.  
  
Alternatively, we don't maintain the parallellism, everything else stays the same, and `as_uint64` will not throw even when `if_uint64` returns false, but `get_uint64` _will still invoke UB_ in that case. And of course `as_int64` and `as_uint64` will be their own very special cases will surprise other people.  
  
BTW, judging by the comments in this issue, the main source of confusion _is not_ that `value::as_uint64` throws when the value stores an `int64_t`, but that the parser stores a positive number from the input as a signed integer rather then an unsigned one.

---

## Comment 18

> Username: pdimov  
> Created at: 2025-11-21 14:14:35 UTC  
> Url: https://github.com/boostorg/json/issues/984#issuecomment-3563197287  

> BTW, judging by the comments in this issue, the main source of confusion is not that value::as_uint64 throws when the value stores an int64_t, but that the parser stores a positive number from the input as a signed integer rather then an unsigned one.  
  
That's quite possible. Unfortunately, if we change it there may be complaints from people who expect int64_t to be stored. :-)
