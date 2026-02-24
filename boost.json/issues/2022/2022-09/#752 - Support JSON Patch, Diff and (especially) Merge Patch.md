# #752 - [Feature Request] Support JSON Patch, Diff and (especially) Merge Patch [Open]

> Username: opelx  
> Created at: 2022-09-17 16:42:15 UTC  
> Updated at: 2023-04-03 08:26:24 UTC  
> Url: https://github.com/boostorg/json/issues/752  

As far I've seen, there is no support for  JSON Patch, Diff and  Merge Patch. Following [JSON Patch and Diff](https://json.nlohmann.me/features/json_patch/) and [JSON Merge Patch](https://json.nlohmann.me/features/merge_patch/) it is an proposed standard [RFC 6902](https://tools.ietf.org/html/rfc6902) and [RFC 7386](https://tools.ietf.org/html/rfc7386).  
IMO, even the JSON merge patch has a practical use case: using a built-in JSON document for configuration/settings and having the user overwrite a small subset of it with a read-in configuration file from the filesystem. This in turn also allows dumping the default values to the filesystem for the user (or even the complete 'patched' settings).

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-09-26 08:38:30 UTC  
> Url: https://github.com/boostorg/json/issues/752#issuecomment-1257691881  

I'm not principally opposed to the idea of supporting JSON Patch, but our current priorities are with different things. So, the quickest way to get this functionality is to write it yourself :wink:   
  
Merge Patch seems like a very simple function. It's more suited for an example rather than a full-blown library function.

---

## Comment 2

> Username: doganulus  
> Created at: 2023-03-30 19:03:04 UTC  
> Url: https://github.com/boostorg/json/issues/752#issuecomment-1490787475  

I agree with the need for JSON Merge Patch as described by the original poster. I think it will be better if the library supports commonly used standards in the JSON ecosystem.  
  
Here I have done a little search and found a related discussion about the merge operation:  
- https://github.com/boostorg/json/issues/754  
  
There I have tested the implementations given by @vinniefalco and @grisumbras but they deal with a limited case of object mergers. The functionality described by [RFC 7386](https://tools.ietf.org/html/rfc7386) requires more than that.   
  
What we need is to:  
- Insert or assign to keys specified in the patch   
- Erase keys having null values specified in the patch  
- Apply the algorithm recursively  
   
See the expected behavior from the RFC 7386 document as follows:  
```cpp  
SECTION("RFC 7386 Example")  
{  
  auto target = boost::json::value{  
    {"title", "Goodbye!"},  
    {"author", {{"givenName", "John"}, {"familyName", "Doe"}}},  
    {"tags", {"example", "sample"}},  
    {"content", "This will be unchanged"}};  
  
  auto patch = boost::json::value{  
    {"title", "Hello!"},  
    {"author", {{"familyName", nullptr}}},  
    {"phoneNumber", "+01-123-456-7890"},  
    {"tags", {"example"}}};  
  
  auto expected = boost::json::value{  
    {"title", "Hello!"},  
    {"author", {{"givenName", "John"}}},  
    {"tags", {"example"}},  
    {"phoneNumber", "+01-123-456-7890"},  
    {"content", "This will be unchanged"}};  
  
  boost::json::merge_patch(target, patch);  
  
  REQUIRE(target == expected);  
}  
```  
  
Please find my candidate implementation below. What do you think? Would you want to add it into the library?   
  
```cpp  
auto merge_patch(value& target, value const& patch) -> value&  
{  
  if(patch.kind() == kind::object) {  
    if(target.kind() != kind::object) {  
      target = object();  
    }  
    for(auto const& p : patch.get_object()) {  
      if(p.value().kind() == kind::null) {  
        target.get_object().erase(p.key());  
      }  
      else {  
        auto* it = target.get_object().if_contains(p.key());  
        if(it != nullptr) {  
          merge_patch(*it, p.value());  
        }  
        else {  
          target.get_object().insert(p);  
        }  
      }  
    }  
  }  
  else {  
    target = patch;  
  }  
  return target;  
}  
```

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-03-31 11:47:34 UTC  
> Updated at: 2023-03-31 11:47:57 UTC  
> Url: https://github.com/boostorg/json/issues/752#issuecomment-1491806529  

Now that I see the semantics of this merge, I am a bit underwhelmed. I use a similar merge function in my personal projects, but mine also appends arrays. This makes me think that this merge is a bit too opinionated. What do you think, @vinniefalco, add this as API or just as an example?

---

## Comment 4

> Username: doganulus  
> Created at: 2023-04-03 08:26:24 UTC  
> Url: https://github.com/boostorg/json/issues/752#issuecomment-1493894961  

Following the practice of Boost.JSON, a `merge_options` struct can be defined to configure the merge operation in the API.   
  
Further I found a more granular control is achieved using `mergeStrategy` keyword in the JSON Schema in some libraries. See a related issue from a JSON Merge library for Python:  
  
- https://github.com/avian2/jsonmerge/issues/28   
  
This way the schema value defines how the merge operation behaves for user-defined keys.
