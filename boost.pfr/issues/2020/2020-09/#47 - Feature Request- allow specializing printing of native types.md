# #47 - Feature Request: allow specializing printing of native types [Closed]

> Username: hadrielk  
> Created at: 2020-09-28 14:17:41 UTC  
> Updated at: 2020-10-05 16:10:49 UTC  
> Closed at: 2020-10-05 16:10:49 UTC  
> Url: https://github.com/boostorg/pfr/issues/47  

Hi,  
First: nice library!  
  
Second: is there a way we can overload or specialize printing of native types? For example the `bool` type - because I want it to print "`true`"/"`false`" not "`1`" or "`0`".  
  
For example, would it be ok to specialize or overload `boost::pfr::detail::quoted_helper()`? It's in the `detail` namespace, so I assume we shouldn't; and besides I haven't gotten it to actually _work_ because of the signature of `quoted_helper()`... but this is more about the general idea of doing so rather than the details.  
  
I realize we can give our streamable a `std::boolalpha`, but doing that at every call site is annoying. And I'm not suggesting `pfr`'s default behavior change either - I'd just like to customize it, if it's possible.(?)

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-09-28 15:29:09 UTC  
> Url: https://github.com/boostorg/pfr/issues/47#issuecomment-700086526  

There are low level functions for iterating over fields and getting field by index. You can use those, to make your own printing functions. Will that do the job?

---

## Comment 2

> Username: hadrielk  
> Created at: 2020-09-28 16:59:09 UTC  
> Url: https://github.com/boostorg/pfr/issues/47#issuecomment-700159578  

Do you mean do something like this?:  
  
```  
///////////////////////////////////////  
// in some header file:  
  
namespace debug::output {  
  
template <class Char, class Traits, class T>  
inline void  
print_each(std::basic_ostream<Char, Traits>& out, const T& v)  
{  
    if constexpr (std::is_same_v<bool, T>)  
    {  
        out << (v ? "true" : "false");  
    }  
    else  
    {  
        using namespace boost::pfr::ops;  
        out << v;  
    }  
}  
  
  
template <class Char, class Traits, class T>  
inline std::basic_ostream<Char, Traits>&  
operator<<(std::basic_ostream<Char, Traits>& out, const T& value)  
{  
    const char* sep = "";  
  
    out << '{';  
  
    boost::pfr::for_each_field(value, [&](const auto& v) {  
        out << std::exchange(sep, ", ");  
        print_each(out, v);  
    });  
  
    return out << '}';  
}  
  
} // namespace debug::output  
  
  
///////////////////////////////////////  
// in some cpp file:  
  
namespace some_other_ns {  
  
std::string  
Options::display() const  
{  
    using namespace debug::output;  
    std::stringstream ss;  
    ss << *this;  
    return ss.str();  
}  
  
} // namespace some_other_ns  
```  
  
yeah, that would work for me for sure. I hadn't thought of just re-recreating `pfr::write()`.  
  
You might want to add an example of that, if you don't have one already. I saw one for streaming a specific user _type_, but not for doing it for native types, and didn't make the mental connection. (it's a Monday ;)
