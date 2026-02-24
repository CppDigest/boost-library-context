# #23 - Add static_string variant without extra space overhead [Closed]

> Username: jm4R  
> Created at: 2021-07-08 08:42:33 UTC  
> Updated at: 2025-12-19 18:40:54 UTC  
> Closed at: 2025-12-19 18:40:54 UTC  
> Url: https://github.com/boostorg/static_string/issues/23  

Current design doesn't guarantee anything about space occupied by `static_string<N>`. User can't assume that `sizeof(basic_static_string<N, CharT>) == (N+1) * sizeof(CharT)` (nor `sizeof(basic_static_string<N, CharT>) == N * sizeof(CharT)` as `N` doesn't take final null-terminator into account) because the class stores additional `size_` field. This is a good thing for `std::string` compatibility which doesn't assume that stored string can't have null characters.  
  
On the other hand, there are still situations where null-terminated cstring-wrapper could be handy. To show the example, we can have a pseudo-POD structure with standarized layout:  
  
```cpp  
struct user_pod  
{  
  std::uint32_t id;  
  char name[64];  
  std::uint32_t something;  
};  
```  
  
Such structures requires using non-modern C functions (like `std::strncpy`) to fill it. This could be replaced with something more handy:  
  
```cpp  
struct user_pod  
{  
  std::uint32_t id;  
  boost::cstring<63> name;  
  std::uint32_t something;  
};  
```  
  
...where `boost::cstring` is a working name of a hypothetical specific class with a strong guaranty that underlying object stores only a `CharT` array of size `N+1` (or `N`, depending on further design) and therefore that objects of that class are trivially copyable.  
  
Implementation of such class doesn't seem to be a hard work - most of existing code could be reused, only `static_string_base` should be conditional. To ilustrate possible design:  
  
[Wandbox link](https://wandbox.org/permlink/IqxvmiwSDDpSTEVV)  
  
```cpp  
#include <iostream>  
#include <cstring>  
  
namespace boost {  
  
namespace detail {  
  
template<std::size_t N, typename CharT, typename Traits>  
class static_string_base  
{  
  using traits_type = Traits;  
  using value_type = typename traits_type::char_type;  
  using size_type = std::size_t;  
public:  
  std::size_t size_impl() const noexcept  
  {  
    return size_;  
  }  
private:  
    size_type size_ = 0;  
    value_type data_[N + 1]{};  
};  
  
template<std::size_t N, typename CharT, typename Traits>  
class static_nullterminated_string_base  
{  
    using traits_type = Traits;  
    using value_type = typename traits_type::char_type;  
public:  
  std::size_t size_impl() const noexcept  
  {  
    return traits_type::length(data_);  
  }  
private:  
    value_type data_[N + 1]{};  
};  
      
} // namespace detail  
  
template<std::size_t N, typename CharT, bool NullTerminated = false, typename Traits = std::char_traits<CharT>>  
class basic_static_string : private std::conditional_t<NullTerminated, detail::static_nullterminated_string_base<N, CharT, Traits>, detail::static_string_base<N, CharT, Traits>>  
{  
  // ...  
};  
  
template<std::size_t N>  
using static_string = basic_static_string<N, char, false, std::char_traits<char>>;  
  
template<std::size_t N>  
using cstring = basic_static_string<N, char, true, std::char_traits<char>>;  
  
}  
  
int main() {  
    constexpr auto SIZE = 64;  
    static_assert(sizeof(boost::static_string<SIZE>) != SIZE + 1);  
    static_assert(sizeof(boost::cstring<SIZE>) == SIZE + 1);  
    return 0;  
}  
```  
  
The question is: could such new functionallity be in scope of this library? Would you accept PR with such extension?

---

## Comment 1

> Username: sdkrystian  
> Created at: 2021-10-25 11:23:03 UTC  
> Url: https://github.com/boostorg/static_string/issues/23#issuecomment-950820981  

Personally I don't believe this falls within the scope of the library, but I'd like to see what @vinniefalco and/or @pdimov have to say on the matter.

---

## Comment 2

> Username: pdimov  
> Created at: 2021-10-25 12:16:45 UTC  
> Url: https://github.com/boostorg/static_string/issues/23#issuecomment-950865872  

`static_cstring` might be useful, but its interface should probably be slightly different.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-10-25 15:07:49 UTC  
> Url: https://github.com/boostorg/static_string/issues/23#issuecomment-951024306  

Without taking an opinion on whether the functionality is in scope, I would say that a new class is preferable to adding more template parameters to the existing class. In any event, we're in the middle of a release cycle so now might not be the best time to add anything :)

---

## Comment 4

> Username: Frafjord  
> Created at: 2025-06-16 13:05:07 UTC  
> Updated at: 2025-06-16 13:08:23 UTC  
> Url: https://github.com/boostorg/static_string/issues/23#issuecomment-2976581711  

When I first saw static_string, I was thinking: do we really need to store the size for small static_strings?  
  
We know the max size of the string at compile time, so we know the cost of strlen. If you create a static_string with max size 10, the cost of strlen is minimal.  
std::string requires that size() has a big O of 1, but we all know that big O makes no sense when n is small.  
  
FBstring had a fun SSO implementation where they let the last byte double as null-terminator and remains. size() would be calculated as ```size() {return capacity - remains;}```. An implementation like this would allow for strings with max size up to 255.  
  
One problem with using the last byte as remains is that an empty string would not be only nulls, the last byte would hold the capacity. This could potentially be fixed by checking the first byte for null in addition to checking the last byte: ```size() {return first_byte == 0 ? 0 : capacity - remains;}```  
  
Another problem I see with this is that if we pass the data() to a function as a buffer, we can't expect the function we call to treat the last byte correctly.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2025-08-24 17:38:55 UTC  
> Url: https://github.com/boostorg/static_string/issues/23#issuecomment-3218259644  

Who needs small static strings?

---

## Comment 6

> Username: jm4R  
> Created at: 2025-08-24 18:12:21 UTC  
> Url: https://github.com/boostorg/static_string/issues/23#issuecomment-3218280403  

> Who needs small static strings?  
  
I do

---

## Comment 7

> Username: Frafjord  
> Created at: 2025-08-24 18:20:42 UTC  
> Url: https://github.com/boostorg/static_string/issues/23#issuecomment-3218284733  

At some point I was working with alphanumeric id's that could be max 6 or 7 characters.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2025-08-24 21:54:42 UTC  
> Url: https://github.com/boostorg/static_string/issues/23#issuecomment-3218401012  

> I was working with alphanumeric id's that could be max 6 or 7 characters.  
  
`std::string` for most implementations has around 17 characters in the small buffer

---

## Comment 9

> Username: Frafjord  
> Created at: 2025-08-24 22:02:57 UTC  
> Url: https://github.com/boostorg/static_string/issues/23#issuecomment-3218405092  

Working with Visual C++ I will fit 15 characters in the small buffer.  
The std::string object will take 32 bytes.  
  
If I only need 6 characters, static_string will let me store them in 8 bytes.  
  
maybe I'm overthinking the size...

---

## Comment 10

> Username: vinniefalco  
> Created at: 2025-08-24 22:44:41 UTC  
> Url: https://github.com/boostorg/static_string/issues/23#issuecomment-3218426332  

`static_string` wasn't particularly designed for the very small string case. It will still work but it wasn't optimized for it and thus has a different set of tradeoffs. The `static_string` API supports all of the modifiers of `std::string`. Do you need all those? Because it sounds like you are just writing a small string and then never changing it again. You might be better served by creating a new type which uses a different set of tradeoffs, specifically for your use-case. You might call it `small_string`.

---

## Comment 11

> Username: jm4R  
> Created at: 2025-08-25 06:13:04 UTC  
> Updated at: 2025-08-25 06:14:15 UTC  
> Url: https://github.com/boostorg/static_string/issues/23#issuecomment-3218963408  

My original request is not about string being small or not. It's about being drop-in replacement to `CharT[N]` just like `std::array` is drop-in replacement for `T[N]`.  
  
Many designs I am familiar with uses `memcpy` / `reinterpret_cast<std::byte*>` as the fastest and reliable, well-defined serialization method for so-called POD data types. It of course works only with some preconditions (as it could be not portable between different architectures, endianess etc.).  
  
Now we have those C++20 designated initializers, `std::span`, `std::as_bytes` and `std::bit_cast` - all of it helps us to make such serialization convenient. But storing strings in such POD types is not convenient at all. Example:  
  
```cpp  
struct window_config  
{  
    std::uint32_t width;  
    std::uint32_t height;  
    bool dark;  
    char title[64];  
};  
static_assert(std::is_trivially_copyable_v<window_config>);  
  
void save()  
{  
    const auto value = window_config{  
        .width = 1024,  
        .height = 768,  
        .dark = true,  
        // .title = "hello", // can't do that  
    };  
    // std::strncpy(value.title, std::size(value.title), "hello"); // not co convenient, also requires mutability  
    const auto bytes = std::as_bytes(std::span{&value, 1});  
    store_to_file_or_send_via_net_or_something(bytes);  
}  
```  
  
Changing char array to `boost::static_string<64>` will not help as the structure has now undefined bit pattern and `static_assert(std::is_trivially_copyable_v<window_config>)` will fail.  
  
Optimization for small strings - like for example comparing 8-bytes strings like `std::uint64_t` would be nice but this is not MY point here.

---

## Comment 12

> Username: pdimov  
> Created at: 2025-08-25 06:36:18 UTC  
> Url: https://github.com/boostorg/static_string/issues/23#issuecomment-3219014908  

`static_string` is not trivially copyable not because of any space overhead. It's not trivially copyable because it has a user-defined copy constructor that only copies the part containing the elements (instead of copying the entire capacity as the default trivial copy constructor would have.)

---

## Comment 13

> Username: jm4R  
> Created at: 2025-08-25 07:27:46 UTC  
> Url: https://github.com/boostorg/static_string/issues/23#issuecomment-3219142495  

@pdimov yes, but as you can see, not being `trivially_copyable` is still not the main problem. The binary incompatibility with `char[N]` is.

---

## Comment 14

> Username: pdimov  
> Created at: 2025-08-25 07:58:48 UTC  
> Url: https://github.com/boostorg/static_string/issues/23#issuecomment-3219227702  

We can make it `char[N+1]` by using the above mentioned trick when N < 256 - which would be a natural extension of the currently applied optimization for N = 0 - but if you want to be able to `memset` it to zero, that's not going to work (all zero would correspond to the legitimate string containing N NUL characters and not to the empty one.)

---

## Comment 15

> Username: gennaroprota  
> Created at: 2025-10-24 08:26:52 UTC  
> Url: https://github.com/boostorg/static_string/issues/23#issuecomment-3441787355  

What should we do with this issue?

---

## Comment 16

> Username: gennaroprota  
> Created at: 2025-12-19 18:40:54 UTC  
> Url: https://github.com/boostorg/static_string/issues/23#issuecomment-3676121593  

I added `basic_static_cstring` in the example/ directory. Feedback welcome. The new template is also tested in the CI, but isn't an official part of the library, yet (as the placement in the example/ directory should make clear).
