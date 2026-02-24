# #687 - [Performance] Non-owning string type [Open]

> Username: jm4R  
> Created at: 2022-03-01 09:05:12 UTC  
> Updated at: 2022-03-02 17:33:39 UTC  
> Url: https://github.com/boostorg/json/issues/687  

Can we have a string type which doesn't make a deep copy? It could be a big performance boost when we serialize some big `Data Transfer Object` to json. For example:  
  
```cpp  
    const std::string s = make_some_string();  
    json::object res;  
    res.emplace("value", json::string_ref(s.c_str())); // string_ref or any name you like  
    // ...  
    return json::serialize(res);  
```  
  
Rapidxml already has simillar distinction between owning and non-owning strings:  
https://rapidjson.org/md_doc_tutorial.html#CreateString  
https://rapidjson.org/structrapidjson_1_1_generic_string_ref.html

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-03-01 13:57:01 UTC  
> Url: https://github.com/boostorg/json/issues/687#issuecomment-1055467149  

Boost.JSON makes certain tradeoffs which advantage some use-cases but disadvantages others. One of the core tradeoffs is that the `json::value` type is copyable and never becomes invalid. If we support a "string reference" variant type, then the utility of `json::value` as a vocabulary type is diminished as users can no longer guarantee that elements of arrays or objects will not become invalid (when the underlying string is destroyed).  
  
If possible, you should structure your code to deal with the native JSON types directly:  
  
```  
json::string s = make_some_string(); // return type is json::string  
json::object res;  
res.emplace( "value", std::move(s) ); // Complexity: constant.  
...  
```

---

## Comment 2

> Username: jm4R  
> Created at: 2022-03-02 08:15:07 UTC  
> Url: https://github.com/boostorg/json/issues/687#issuecomment-1056533752  

@vinniefalco I see your point. Having safe type like current `json::value` is indeed convenient. But on the other hand the solution you presented break's the whole idea of encapsulation. One would have to completely change application logic design to use `json::string` instead of `std::string` all around the code. This might be impossible when using libraries that produces `std::string` (like `fmtlib` for example). Encapsulation of serialization layer inside separate module (possibly separate cmake target) would be impossible.  
  
Some (but not all) of the problems could be resolved just by introducing `json::string(std::string&&)` constructor that takes ownership of underlying storage, yet I don't know if this is possible with current design.  
  
Maybe modification of `json::value` is indeed not a good idea, but is it possible that in the feature we could develop different solution, something like `json::unsafe_value` that will support this feature? I think it is worth discussion and consideration.

---

## Comment 3

> Username: grisumbras  
> Created at: 2022-03-02 10:39:09 UTC  
> Url: https://github.com/boostorg/json/issues/687#issuecomment-1056774650  

I'd there's more chance we create an API for direct JSON serialization that sidesteps value over allowing string_view in value. The point of value is to own its data.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-03-02 17:33:39 UTC  
> Url: https://github.com/boostorg/json/issues/687#issuecomment-1057193690  

One of the axioms upon which the library is based is "there exists no single set of tradeoffs that will satisfy all JSON use-cases." Boost.JSON is designed to provide a container type which behaves as a value, that operates safely and correctly in all the ways that C++ developers expect such types to work. That is, they are intuitively movable, copyable, constructible, and the `json::value` operates similarly to a `std::variant` over the JSON subtypes (array, object, string, and native types). Compare this with RapidJSON where assignments have reference semantics instead of value semantics, and the allocator has to be passed in with every mutating operation.  
  
In addition to emphasizing performance and compactness, the interface normalizes (or homogenizes) the library types. That is, they do not provide any user customization (this is why there are no templates). You can't choose the type of string or the type of hash table used for objects. A Boost.JSON array is the same for every program, on every platform. It can't be configured. Compare this with nlohmann whose basic_value is templated on 8 types.  
  
In other words Boost.JSON types are suitable as vocabulary types. There will always be feature requests such as the one described in the issue, where users desire a different set of tradeoffs from those chosen by the library. These are valid requests, but I don't think they belong in Boost.JSON. Or rather, they should be explored with a completely different set of types and containers. Essentially a different JSON library, which chooses its tradeoffs to achieve a particular goal.
