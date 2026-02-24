# #1101 - Request for JCS serialization mode (rfc8785) [Open]

> Username: slnj  
> Created at: 2025-08-27 04:23:08 UTC  
> Updated at: 2025-08-30 00:34:23 UTC  
> Url: https://github.com/boostorg/json/issues/1101  

I was wondering if there is any interest in implementing an incorporating JCS serialization (https://datatracker.ietf.org/doc/html/rfc8785) to be able to hash/sign json documents.  
  
I understand something like this should do it, but a lowlevel implementation accessing internal structure should be much better:  
  
```cpp  
#pragma once  
#include <boost/json.hpp>  
#include <vector>  
#include <string>  
#include <algorithm>  
#include <ostream>  
  
namespace jcs // keep our code out of boost::json to avoid ODR surprises  
{  
namespace detail {  
  
namespace json = boost::json;  
  
// Writes a JSON string literal (with quotes and escapes) to the stream  
inline void write_string_literal(std::ostream& os, boost::json::string_view sv) {  
    // Construct a temporary boost::json::string and let Boost do the escaping  
    json::string tmp{sv.data(), sv.size()};  
    os << tmp; // operator<< will serialize with quotes and escapes  
}  
  
inline void write_value_jcs(std::ostream& os, const json::value& v); // fwd  
  
inline void write_object_jcs(std::ostream& os, const json::object& obj) {  
    // Collect pointers to members and sort by key (lexicographic UTF-8)  
    std::vector<std::pair<json::string_view, const json::value*>> members;  
    members.reserve(obj.size());  
    for (const auto& kv : obj) {  
        members.emplace_back(kv.key(), &kv.value());  
    }  
  
    std::sort(members.begin(), members.end(),  
              [](const auto& a, const auto& b) {  
                  // UTF-8 bytewise comparison is acceptable for JCS key ordering  
                  return a.first < b.first;  
              });  
  
    os << '{';  
    bool first = true;  
    for (const auto& [k, vp] : members) {  
        if (!first) os << ',';  
        first = false;  
  
        // "key":  
        write_string_literal(os, k);  
        os << ':';  
  
        // value  
        write_value_jcs(os, *vp);  
    }  
    os << '}';  
}  
  
inline void write_array_jcs(std::ostream& os, const json::array& arr) {  
    os << '[';  
    bool first = true;  
    for (const auto& e : arr) {  
        if (!first) os << ',';  
        first = false;  
        write_value_jcs(os, e); // preserve order; no sorting in JCS  
    }  
    os << ']';  
}  
  
inline void write_value_jcs(std::ostream& os, const json::value& v) {  
    if (v.is_object()) {  
        write_object_jcs(os, v.as_object());  
    } else if (v.is_array()) {  
        write_array_jcs(os, v.as_array());  
    } else {  
        // Scalars: let Boost.JSON print compact JSON (numbers/strings/bool/null)  
        // This uses Boost's serializer for correct numeric format and string escapes  
        os << v;  
    }  
}  
  
} // namespace detail  
  
// Public API: write a boost::json::value in JCS-style to a std::ostream  
inline void serialize_jcs_to_stream(const boost::json::value& v, std::ostream& os) {  
    detail::write_value_jcs(os, v);  
}  
  
// Convenience: return std::string (uses ostringstream)  
inline std::string serialize_jcs(const boost::json::value& v) {  
    std::ostringstream oss;  
    serialize_jcs_to_stream(v, oss);  
    return oss.str();  
}  
  
} // namespace jcs  
  
int main() {  
    // Unordered JSON  
    boost::json::value val = boost::json::parse(R"({"z":2,"a":{"c":3,"b":2},"x":1})");  
  
    // JCS serialization  
    std::string out = jcs::serialize_jcs(val);  
  
    std::cout << sign_stub(out) << "\n";  
}

---

## Comment 1

> Username: grisumbras  
> Created at: 2025-08-27 06:53:07 UTC  
> Url: https://github.com/boostorg/json/issues/1101#issuecomment-3226988035  

After reading the spec, I think it's to a largely incompatible with our serializer. The spec assumes knowledge of the encoding of JSON strings. Our serializer does not. You can use any ASCII-based encoding and the result will be in that encoding. JSC, on the other hand, uses encoding knowledge in two ways:  
1. When sorting object keys it converts the key to a UTF-16 string.  
2. When producing output it converts any string to a UTF-8 string.  
  
Btw, your snippet doesn't convert the keys to UTF-16 for comparison.   
  
Other modifications that would be required:  
* Object keys lexicographical sorting.  
* Rejecting NaN and Infinity.  
* Possibly we'd have to use a different number formatter, although that's not immediately clear.  
  
Object key sorting requires a (potentially quite significant) memory allocation.  
  
So, it would be a mistake in my opinion to use the current serializer for this. But if the issue will gather interest, I may return to it in the future and write a separate serializer for Canonicalization Scheme. Note though, that there is still a major hurdle of figuring out what encoding your strings use. We will probably just require your strings to be UTF-8.
