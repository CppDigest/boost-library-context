# #740 - double check review feedback [Open]

> Username: vinniefalco  
> Created at: 2022-08-21 00:48:27 UTC  
> Updated at: 2023-06-02 08:50:50 UTC  
> Url: https://github.com/boostorg/json/issues/740  

Did all of the items in this review get addressed? Alan can help with the docs if necessary (the friend stuff) https://lists.boost.org/Archives/boost//2020/09/249834.php

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-08-28 03:39:32 UTC  
> Updated at: 2023-06-02 08:50:50 UTC  
> Url: https://github.com/boostorg/json/issues/740#issuecomment-1229367079  

## DOCUMENTATION  
1. [x] 1. Add reference to RFC 7159 (https://www.rfc-editor.org/info/rfc7159). It  
allows implementations to set limits on the range and precision of numbers  
accepted.  
  
2. [x] 2. The documentation for array object and index mention "Satisfies the  
requirements of ContiguousContainer, ReversibleContainer, and  
SequenceContainer" These requirements are never defined or referenced. The  
user will not even know if you defined them or if they are "commonly  
known". Maybe add link to cppreference.com:  
  
3. [x] 3. String has two sections "Member Functions":  
https://master.json.cpp.al/json/ref/boost__json__string.html  
  
4. [x] 4. Documentation for value::operator= seems corrupt:  
https://master.json.cpp.al/json/ref/boost__json__value/operator_eq_/overload4.html  
    * it has sections with no content  
    * It is an unconstrained template, it look like it can accept any type  
  
5. [x] 5. Examples of input JSON-s contain a lot of escape characters in string  
literals, like in https://master.json.cpp.al/json/usage/conversion.html  
This makes it difficult to read. Consider using raw string literals.  
Instead of  
    ```c++  
    assert( serialize( jv ) == "{\"x\":4,\"y\":1,\"z\":4}" );  
    ```  
    We would have  
    ```c++  
    assert( serialize( jv ) == R"({"x":4,"y":1,"z":4})" );  
    ```  
  
6. [ ] 6. Some functions (like `operator==` or `swap(x, y)`) are naturally part of  
class interface, even though they are not member functions. It is annoying  
and confusing that rather than being listed along with the class they  
belong to they are instead listed separately as "free functions"  
The docs are not uniform about this issue. For `json::value` `operator==` is  
listed as friends along with other member functions, but not 2-argument  
`swap()`.  
  
7. [x] 7. In https://master.json.cpp.al/json/usage/conversion.html we read:  
"Given the following definition of `customer::customer( const value& )`  
[...] Objects of type customer can be converted *to* and from value".<br>  
This "to" is surprising here. How can a constructor converting from `value`  
to `customer` could be used to convert `customer` to `value`?  
  
8. [ ] 8. As discussed in another thread, documentation would benefit from the  
info on implementation-limits-related design choices.  
    1. Any `json::value` that you can build can be serialized and then  
       deserialized, and you are guaranteed that the resulting `json::value` will be  
       equal to the original.  
    2. JSON inputs where number values cannot be represented losslessly in  
       `uint64_t`, `int64_t` and `double`, may render different values when parsed and  
       then serialized back, and for extremely big number values can even fail to  
       parse.  
    3. Whatever JSON output you can produce with this library, we guarantee it  
       can be passed by any common JSON implementation (probably also based on  
       `uint64_t` + `int64_t` + `double` implementation.  
  
## DESIGN  
1. [ ] 1. The handling of infinities and NaN's needs to be addressed. Currently, I  
can do:  
    ```c++  
    json::serialize(std::numeric_limits<double>::infinity())  
    ```  
    and it produces an invalid JSON. You cannot easily make a precondition on  
    `json::value` assignment/construction because you expose the double as a  
    mutable member:  
    ```c++  
    void fun(double x)  
      // precondition x is not infinity or nan  
    {  
      json::value v = x; // ok  
      v.as_double() *= 2.0; // this may produce infinity  
    }  
    ```  
    So maybe you need a precondition in the serializer.  
2. [ ] 2. Boost.JSON provides its own string type. This begs a lot of design  
questions for `json::string`. Is it a better replacement for `std::string`?  
`std::string` provides the following capability:  
    ```c++  
    void set(std::string& s, char v, char x, char z, char k)  
    {  
       if (cond)  
         s = {v, k};  
       else  
         s = {v, x, z, k};  
    }  
    ```  
    `json::string` does not. How do I do a similar thing with `json::string`?  
3. [x] 3. `string::swap()` has a strange precondition: you cannot swap an object  
with itself  
https://master.json.cpp.al/json/ref/boost__json__string/swap.html  
This is a really unexpected and potentially dangerous, and at minimum  
requires a very strong rationale. This would be the first swap function in  
std or Boost that has a precondition. While discussing a somewhat related  
issue (on the guarantees of the moved-from state) in the LEWG, someone  
remarked that there was at least one STL implementation that did a  
self-swap for convenience of implementation.  
Is it really necessary?  
  
## IMPLEMENTATION  
1. [ ] 1. Values of type `double` representing Infinity and NaN can be stored in  
`json::value` and when later serialized, they render a malformed JSON  
output. `json::value` should either validate special values of `double` or  
indicate as precondition that they should not be assigned. Alternatively,  
the serializer should either validate this or indicate as precondition.  
2. [x] 2. `tag_invoke()` takes tag arguments by reference, does it have to? Passing  
by reference requires the `constexpr` variables to be instantiated and their  
address assigned.  
3. [x] 3. Customization through ADL-discovered overloads can cause ODR violations.  
There is no superior alternative though. But maybe put a warning in the  
docs that the user must make sure that all translation units must see the  
same set of overloads.  
4. [x] 4. I do not like the 2-state-switch mechanics of `BOOST_JSON_STANDALONE`. It  
assumes that a user either wants a no-Boost version or that she wants a  
package: all Boost components.  
But what if I am using C++14 and I want to use `std::error_code` and  
Boost-emulated `memory_resource` and `string_view`? What If I use Boost version  
and C++17 and want to use `std::string_view`?  
Maybe you can additionally provide macros for turning Boost-emulation  
libraries one-by-one? I mean, one for `string_view`, one for `error_code`, and  
one for `memory_resource`.  
5. [x] 5. The header only mode -- I really appreciate it. It is good for initial  
experiments.

---

## Comment 2

> Username: grisumbras  
> Created at: 2022-08-28 04:57:10 UTC  
> Url: https://github.com/boostorg/json/issues/740#issuecomment-1229376474  

My comments on unaddressed items:  
  
Documentation  
1. Created issue #742  
5. Created issue #743  
6. Currently Docca only supports tying a free function to a class via friend declaration. We can make free functions that are parts of a type's extended API friends of that type. Does it worth it?  
8. Created issue #744  
  
Design  
1. Issues #350, #397  
2. The principal reason for why `json::string` is used is described in [FAQ](https://www.boost.org/doc/libs/develop/libs/json/doc/html/json/frequently_asked_questions.html). The specific issue of missing `initializer_list` constructor begs the question if the constructor is useful.  
3. Created issue #745  
  
Implementation  
1. Issues #350, #397  
2. We do actually need tags passed by reference, since a feature (conversion customization without physical dependency on JSON) depends on it.  
3. I don't think this would be useful. I would expect anyone knowing what ODR is to have a basic understanding of what can result in its violation. So we have people who know about ODR, they should understand how overload resolution can violate ODR. And we have people who don't know about ODR, they first require a description of ODR itself.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-08-28 14:45:23 UTC  
> Url: https://github.com/boostorg/json/issues/740#issuecomment-1229476219  

Alan can help with 6 (or just copy what we did in URL)

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-08-28 14:54:51 UTC  
> Url: https://github.com/boostorg/json/issues/740#issuecomment-1229478299  

> String has two sections "Member Functions":  
  
This was a docca bug and should be fixed already
