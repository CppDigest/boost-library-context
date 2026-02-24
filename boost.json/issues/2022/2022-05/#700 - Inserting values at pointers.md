# #700 - Inserting values at pointers [Closed]

> Username: doganulus  
> Created at: 2022-05-04 18:22:22 UTC  
> Updated at: 2023-01-02 10:43:32 UTC  
> Closed at: 2023-01-02 10:43:32 UTC  
> Url: https://github.com/boostorg/json/issues/700  

Currently trying out the new JSON pointer implementation and I think this would be very useful in many cases.  
  
One thing I have needed is inserting or assigning arbitrary JSON values at locations specified by JSON pointers. Is it currently possible? How can I achieve it in the current state?

---

## Comment 1

> Username: sehe  
> Created at: 2022-05-04 18:33:00 UTC  
> Url: https://github.com/boostorg/json/issues/700#issuecomment-1117672593  

Probably a duplicate of https://github.com/boostorg/json/issues/480

---

## Comment 2

> Username: pdimov  
> Created at: 2022-05-04 18:38:12 UTC  
> Url: https://github.com/boostorg/json/issues/700#issuecomment-1117676737  

Not a duplicate. That issue is about finding an element by its path, and this one is about inserting an element at a specified path.

---

## Comment 3

> Username: grisumbras  
> Created at: 2022-05-04 19:10:56 UTC  
> Url: https://github.com/boostorg/json/issues/700#issuecomment-1117716045  

Just to be sure, this is about creating intermediary objects, right? Do you also have any suggestions on how to deal with non-existant elements of arrays?

---

## Comment 4

> Username: doganulus  
> Created at: 2022-05-04 20:15:17 UTC  
> Updated at: 2022-05-05 20:06:31 UTC  
> Url: https://github.com/boostorg/json/issues/700#issuecomment-1117851201  

Yes, non-existent objects are to be created at the insertion. For example,  
  
```cpp  
value jv1 {{"a": {{"b", 1}}}};  
value jv2 {{"c": {{"d", 2}}}};  
  
jv1.insert_or_assign("/x", jv2);  
jv1.insert_or_assign("/a/b", jv2);  
  
assert(jv1.at_pointer"/x/c/d" == 2)     // new value inserted at previously non-existent path /x  
assert(jv1.at_pointer("/a/b/c/d") == 2) // new value assigned at the existing path /a/b  
```  
  
For non-existent array elements, I think `std::vector` assignment behavior might be preferable. Assign the value if the index is in range or else return an out-of-range/not-found error. Inserting to the back of an array can be specified using the `-` character according to [RFC 6901](https://datatracker.ietf.org/doc/html/rfc6901#section-4). ~~A helper function to get the size of a `json::array` at a given pointer path may be needed as well.~~ Getting the size of objects/arrays via pointers is currently possible.

---

## Comment 5

> Username: doganulus  
> Created at: 2022-05-04 21:30:10 UTC  
> Updated at: 2022-05-05 20:06:18 UTC  
> Url: https://github.com/boostorg/json/issues/700#issuecomment-1117955387  

Further investigations with the current API:  
  
For objects:  
```cpp  
value jv = {{"abc", {{"xyz", {12, 13, 15}}}}};  
  
jv.at_pointer("/abc/xyz") = {"123", "1231"}; // works as desired  
jv.at_pointer("/abc/klm") = {"123", "1231"}; // does not work but desired  
```  
  
For arrays:  
```cpp  
value jv = {{"abc", {{"xyz", {12, 13, 15}}}}};  
  
jv.at_pointer("/abc/xyz/0") = {"123", "1231"}; // works as desired  
jv.at_pointer("/abc/xyz/3") = {"123", "1231"}; // does not work as desired  
jv.at_pointer("/abc/xyz/-") = {"123", "1231"}; // does not work but desired  
```

---

## Comment 6

> Username: pdimov  
> Created at: 2022-05-04 23:03:05 UTC  
> Url: https://github.com/boostorg/json/issues/700#issuecomment-1118010827  

We're having differences of opinion on what `jv.insert_at_pointer("/a/2", 5)` needs to create, an array or an object. @grisumbras thinks it should create an object and set its `"2"` key to 5. I find this consistent but kind of not really doing what one would want in the majority of cases. It seems to me that it should create an array and set its third element to 5 (leaving the first two `null`).

---

## Comment 7

> Username: grisumbras  
> Created at: 2022-05-05 11:30:31 UTC  
> Url: https://github.com/boostorg/json/issues/700#issuecomment-1118444694  

There are 3 questions we need to answer to decide on the behaviour.  
  
1. Whether to replace `null`s when creating intermediaries.  
2. When creating intermediaries whether to treat a number segment of the path as an implication of array index or object key.  
3. Whether to extend arrays with null elements when a referenced index does not exist.  
  
Positive answer to question 3 also implies positive answer to question 1, as  otherwise there's very little utility in it.  
Personally, I am leaning towards 1 - yes, 2 - no, 3 - no. The only way to create an intermediary array would be using `-` segement. That would also be the only way to extend an array.  
  
First of all, I very much don't like option 3. I'm also not sure if it's actually needed, because `-` segment can be used. Without option 3 there will be no way reason to _create_ arrays using number segments, just use `-`. So option 2 becomes irrelevant.  
  
Meanwhile, option 1 is very useful IMO, doesn't create inconsistencies and probably even simplifies implementation.  
  
Examples with my approach:  
```cpp  
value jv = parse(R"( {"a": null, "x": [2]}  )");  
jv.assign_at_pointer("/a/b/1", 3); // {"a": {"b": {"1", 3}}, "x": [2]}  
jv.assign_at_pointer("/x/-", 4);   // {"a": {"b": {"1", 3}}, "x": [2, 4]}  
jv.assign_at_pointer("/y/-", 5);   // {"a": {"b": {"1", 3}}, "x": [2, 4], "y": [5]}  
jv.assign_at_pointer("/z/0", 6);   // {"a": {"b": {"1", 3}}, "x": [2, 4], "y": [5], "z": {"0": 6}}  
jv.assign_at_pointer("/x/1", 7);   // {"a": {"b": {"1", 3}}, "x": [2, 7], "y": [5], "z": {"0": 6}}  
jv.assign_at_pointer("/x/3", 8);   // error: we don't extend arrays by more than one element  
```  
  
So, to help me understand if there's a case for supporting option 3, can anyone present a realistic use case?

---

## Comment 8

> Username: pdimov  
> Created at: 2022-05-05 12:04:26 UTC  
> Url: https://github.com/boostorg/json/issues/700#issuecomment-1118469726  

There's no use case for (1) if you don't have (2)+(3).

---

## Comment 9

> Username: pdimov  
> Created at: 2022-05-05 13:36:31 UTC  
> Url: https://github.com/boostorg/json/issues/700#issuecomment-1118562489  

(1) is useful if one has (3) and wants to maintain order independence as in  
```  
value jv = array();  
jv.assign_at_pointer("/1/y", 5);  
jv.assign_at_pointer("/0/x", 3);  
```  
giving the same result as  
```  
jv.assign_at_pointer("/0/x", 3);  
jv.assign_at_pointer("/1/y", 5);  
```  
because there's an implicit `null` created by the first line.  
  
On second thought, though, I no longer favor (3) because it makes denial of service attacks trivial (`"/123456789"`). So I agree that numeric indices should be constrained to [0, size()] (with `-` being the same as `size()`.)

---

## Comment 10

> Username: doganulus  
> Created at: 2022-05-05 14:47:22 UTC  
> Updated at: 2022-05-05 20:05:49 UTC  
> Url: https://github.com/boostorg/json/issues/700#issuecomment-1118646170  

(1) yes, (3) no.   
  
Still, there might be some rough edges regarding (1) though as it is not only limited to `null` values but all values that are not objects.   
  
Also I think the minus sign shouldn't create a new array but treat it as a regular key if the stem is not an array. For example,  
  
```cpp  
value jv = parse(R"( {"x": [2]} )");  
jv.assign_at_pointer("/x/-", 5);   // {"x": [2, 5]}  
jv.assign_at_pointer("/y/-", 5);   // {"x": [2], "y": {"-" : 5}}  
```  
  
The previous behavior can be obtained via assigning an array directly to the stem:  
```cpp  
value jv = parse(R"( {"x": [2]} )");  
jv.assign_at_pointer("/y", {5});   // {"x": [2], "y": [5]}  
jv.assign_at_pointer("/y/-", 6);   // {"x": [2], "y": [5,6]}  
```

---

## Comment 11

> Username: doganulus  
> Created at: 2022-05-05 15:15:34 UTC  
> Updated at: 2022-05-05 20:08:10 UTC  
> Url: https://github.com/boostorg/json/issues/700#issuecomment-1118681769  

RapidJSON implements the behavior (3). The program below is problematic as @pdimov points.  
  
```cpp  
#include "rapidjson/document.h"  
#include "rapidjson/pointer.h"  
  
using namespace rapidjson;  
  
int main()  
{  
  const char* json = R"({"my_obj": { "my_arr": [10,11,12]}})";  
  Document d;  
  d.Parse(json);  
  
  Document v;  
  v.Parse("20");  
  SetValueByPointer(d, "/my_obj/my_arr/999999999", v);  
  
  return 0;  
}  
```

---

## Comment 12

> Username: grisumbras  
> Created at: 2022-05-07 21:04:42 UTC  
> Url: https://github.com/boostorg/json/issues/700#issuecomment-1120290182  

Interesting. So RapidJSON says yes to 1, 2, and 3. And even converts other kinds of values to arrays and objects, not just nulls. Maybe we need 2 kinds of functions: one conservative and one "helpful".

---

## Comment 13

> Username: grisumbras  
> Created at: 2022-08-04 08:55:29 UTC  
> Updated at: 2022-08-04 08:59:47 UTC  
> Url: https://github.com/boostorg/json/issues/700#issuecomment-1204964786  

@doganulus @pdimov can you check out #713 to see if you agree with the choices made in that implementation.  
The choices are:  
  
1. Nulls are replaced when creating intermediate elements.  
2. When creating intermediate elements segments are always treated as object keys, (in other words, only objects are created as intermediates).  
3. Arrays are extended by 1 element at most.  
  
In other words, the answers to questions discussed previously are 1: yes, 2: no, 3: no.

---

## Comment 14

> Username: grisumbras  
> Created at: 2022-11-16 12:45:08 UTC  
> Url: https://github.com/boostorg/json/issues/700#issuecomment-1316965381  

@doganulus can you check out #713 again. It has been rewritten to be customisable. Does the provided functionality accomodate your use cases?

---

## Comment 15

> Username: doganulus  
> Created at: 2022-11-16 19:14:45 UTC  
> Url: https://github.com/boostorg/json/issues/700#issuecomment-1317541224  

One question: Can we set values at intermediate keys as follows?  
  
```  
jv = value();  
result = &jv.set_at_pointer("/a/b/c", "m"); //  
result = &jv.set_at_pointer("/a/b", "n");  
```  
  
Or the option `replace_any_scalar` just implies that we can only replace the leaf values?

---

## Comment 16

> Username: grisumbras  
> Created at: 2022-11-16 19:21:31 UTC  
> Url: https://github.com/boostorg/json/issues/700#issuecomment-1317549685  

No, that option controls whether only nulls or any non-container can be replaced.

---

## Comment 17

> Username: grisumbras  
> Created at: 2022-11-16 19:22:46 UTC  
> Updated at: 2022-11-16 19:24:23 UTC  
> Url: https://github.com/boostorg/json/issues/700#issuecomment-1317551398  

```c++  
value jv = 1;  
jv.set_at_pointer("/a/b/c", "m"); // exception  
jv.set_at_pointer("/a/b/c", "m", {.replace_any_scalar = true}); // success  
```
