# #570 - Add hash_value overloads to also support boost::hash in addition to std::hash [Closed]

> Username: pdimov  
> Created at: 2021-05-07 21:56:49 UTC  
> Updated at: 2023-01-02 09:57:50 UTC  
> Closed at: 2023-01-02 09:57:50 UTC  
> Url: https://github.com/boostorg/json/issues/570  

Now that https://github.com/boostorg/json/pull/522 has added `std::hash` support for the JSON types, adding `hash_value` overloads that simply return the result of `std::hash` (or vice versa) will also enable support for `boost::hash`.  
  
Apart from the fact that a Boost library should integrate with other Boost libraries, `boost::hash` is also more convenient in that it supports the standard containers, `std::pair` and `std::tuple`.

---

## Comment 1

> Username: pdimov  
> Created at: 2022-11-09 03:00:44 UTC  
> Url: https://github.com/boostorg/json/issues/570#issuecomment-1308135866  

The `boost::hash` defaults mostly work, so the only thing that's needed is  
```  
namespace boost  
{  
namespace json  
{  
  
std::size_t hash_value( value const& jv ) noexcept  
{  
    std::size_t seed = 0;  
  
    boost::hash_combine( seed, jv.kind() == kind::int64? kind::uint64: jv.kind() );  
    boost::json::visit( [&]( auto const& v ){ boost::hash_combine( seed, v ); }, jv );  
  
    return seed;  
}  
  
std::size_t hash_value( key_value_pair const& kv ) noexcept  
{  
    std::size_t seed = 0;  
  
    boost::hash_combine( seed, kv.key() );  
    boost::hash_combine( seed, kv.value() );  
  
    return seed;  
}  
  
} // namespace json  
  
namespace container_hash  
{  
  
template<class T> struct is_unordered_range;  
template<> struct is_unordered_range< json::object >: std::true_type {};  
  
} // namespace container_hash  
  
} // namespace boost  
```  
and https://github.com/boostorg/container_hash/issues/29. Although `key_value_pair` should also work out of the box, https://github.com/boostorg/container_hash/issues/30.

---

## Comment 2

> Username: pdimov  
> Created at: 2022-11-28 20:28:20 UTC  
> Url: https://github.com/boostorg/json/issues/570#issuecomment-1329724024  

With the current `develop` of `container_hash`, the implementation is  
```  
namespace boost  
{  
namespace json  
{  
  
std::size_t hash_value( value const& jv ) noexcept  
{  
    std::size_t seed = 0;  
  
    boost::hash_combine( seed, jv.kind() == kind::int64? kind::uint64: jv.kind() );  
    boost::json::visit( [&]( auto const& v ){ boost::hash_combine( seed, v ); }, jv );  
  
    return seed;  
}  
  
} // namespace json  
  
namespace container_hash  
{  
  
template<class T> struct is_unordered_range;  
template<> struct is_unordered_range< json::object >: std::true_type {};  
  
} // namespace container_hash  
} // namespace boost  
```  
A simple test program is  
```  
int main()  
{  
    boost::json::string s1( "test" );  
    std::cout << "s1: " << boost::hash<boost::json::string>()( s1 ) << std::endl;  
  
    std::cout << "s1: " << boost::hash<boost::core::string_view>()( s1 ) << std::endl;  
  
    boost::json::array a1{};  
    std::cout << "a1: " << boost::hash<boost::json::array>()( a1 ) << std::endl;  
  
    boost::json::array a2{ 1, 2 };  
    std::cout << "a2: " << boost::hash<boost::json::array>()( a2 ) << std::endl;  
  
    std::vector<boost::json::value> w2{ 1, 2 };  
    std::cout << "w2: " << boost::hash<std::vector<boost::json::value>>()( w2 ) << std::endl;  
  
    boost::json::object o1{};  
    std::cout << "o1: " << boost::hash<boost::json::object>()( o1 ) << std::endl;  
  
    boost::json::object o2{ { "a", 1 }, { "b", 2 } };  
    std::cout << "o2: " << boost::hash<boost::json::object>()( o2 ) << std::endl;  
  
    std::unordered_map<std::string, boost::json::value> m2{ { "a", 1 }, { "b", 2 } };  
    std::cout << "m2: " << boost::hash<std::unordered_map<std::string, boost::json::value>>()( m2 ) << std::endl;  
  
    boost::json::object o3{ { "b", 2 }, { "a", 1 } };  
    std::cout << "o3: " << boost::hash<boost::json::object>()( o3 ) << std::endl;  
  
    boost::json::value v1;  
    std::cout << "v1: " << boost::hash<boost::json::value>()( v1 ) << std::endl;  
  
    boost::json::value v2( 0 );  
    std::cout << "v2: " << boost::hash<boost::json::value>()( v2 ) << std::endl;  
  
    boost::json::value v3( 0.0f );  
    std::cout << "v3: " << boost::hash<boost::json::value>()( v3 ) << std::endl;  
  
    boost::json::value v4( s1 );  
    std::cout << "v4: " << boost::hash<boost::json::value>()( v4 ) << std::endl;  
  
    boost::json::value v5( a1 );  
    std::cout << "v5: " << boost::hash<boost::json::value>()( v5 ) << std::endl;  
  
    boost::json::value v6( o1 );  
    std::cout << "v6: " << boost::hash<boost::json::value>()( v6 ) << std::endl;  
}  
```  
which for me prints  
```  
s1: 13351117993745878004  
s1: 13351117993745878004  
a1: 0  
a2: 16266386247662798537  
w2: 16266386247662798537  
o1: 0  
o2: 442378051458334090  
m2: 442378051458334090  
o3: 442378051458334090  
v1: 17303869719317669699  
v2: 3053310743997383808  
v3: 4843021734237008781  
v4: 18118795333961168141  
v5: 133998018653947411  
v6: 2531748774112664186  
```  
However, since `value` allows quite a bit of implicit conversions, it would be better to either define `hash_value` inside `value` as a hidden friend, or make it a template:  
```  
template<class T>  
inline  
typename std::enable_if<std::is_same<T, value>::value, std::size_t>::type  
hash_value( T const& jv ) noexcept  
{  
    std::size_t seed = 0;  
  
    boost::hash_combine( seed, jv.kind() == kind::int64? kind::uint64: jv.kind() );  
    boost::json::visit( [&]( auto const& v ){ boost::hash_combine( seed, v ); }, jv );  
  
    return seed;  
}  
```  
This implementation relies on the hash values of `int64_t` and `uint64_t` being the same when the value is representable in both. I've added a test to ContainerHash to ensure that this property is preserved.  
  
Once this is in place, the current `std::hash` implementations can be dropped and replaced with derivation from `boost::hash`.
