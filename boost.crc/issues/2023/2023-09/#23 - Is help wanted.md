# #23 - Is help wanted? [Open]

> Username: YarikTH  
> Created at: 2023-09-22 22:34:25 UTC  
> Updated at: 2024-04-14 16:19:27 UTC  
> Url: https://github.com/boostorg/crc/issues/23  

I'm interested in two topics for boost::crc:  
1. Add optional standalone support. Currently all crc's dependencies can be supplied by standard library (except boost::uint_t<>) starting from a certain standard  
2. Add constexpr support, so crc can be calculated at compile time  
  
Is it any chance it can be accepted in this library or it is better not to try and make my own personal fork with desired changes and call it a day?  
  
It is worrying sign that some of existing issues and PR has absolutely no feedback or activity, so I fear making a proper PR could be waste of time if there is nobody to process it.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-04-14 01:15:42 UTC  
> Url: https://github.com/boostorg/crc/issues/23#issuecomment-2053834323  

How standalone do you need the library to be? Is a dependency on Config acceptable, or does it have to be really standalone?  
  
(There's not that much difference between one FetchContent and two.)

---

## Comment 2

> Username: YarikTH  
> Created at: 2024-04-14 07:07:34 UTC  
> Updated at: 2024-04-14 07:10:36 UTC  
> Url: https://github.com/boostorg/crc/issues/23#issuecomment-2053939594  

boost::crc made in a such way, that dependency on boost::config and boost::integer is barely used. So it has a potential to be like [boost::QVM](https://www.boost.org/doc/libs/1_84_0/libs/qvm/doc/html/index.html) and [boost::LEAF](https://www.boost.org/doc/libs/1_84_0/libs/leaf/doc/html/index.html) that both are boost libraries, but in the form of "Portable single-header format, no dependencies.".  
  
In such case, boost::crc can be dropped as a single header in any project that for some reason can't use boost via package manager or via submodule/FetchContent.  
  
I don't know if it is widely desired, because as I understand boost users usually don't leave feedback on their usage and usecaces. If it needed for me only, I can easily do so via downloading boost::crc header and applying my own patch that will do the work.  
  
P.S. it can be portable in C++11+ mode only. Otherwise it require some additional polyfill dependencies from boost.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-04-14 12:48:28 UTC  
> Url: https://github.com/boostorg/crc/issues/23#issuecomment-2054043449  

It's very easy to remove the current dependency on Config (and I in fact have done so), but this will make implementing `constexpr` harder, as various workarounds would be needed for C++11 compilers and older compilers, which are currently supported.  
  
And once we make CRC truly standalone and single header, we won't be able to switch back.

---

## Comment 4

> Username: YarikTH  
> Created at: 2024-04-14 13:04:22 UTC  
> Updated at: 2024-04-14 13:05:12 UTC  
> Url: https://github.com/boostorg/crc/issues/23#issuecomment-2054055596  

As I can see, implementing `constexpr` for current CRC implementation is very hard, even without compatibility problems. Because it is implemented using static arrays with precalculated values that is not compatible with `constexpr`. Either all the code (or at least some) should be doubled to have separate `constexpr` and non-`constexpr` versions, or, more realistically, C++20 [std::is_constant_evaluated](https://en.cppreference.com/w/cpp/types/is_constant_evaluated) is required.

---

## Comment 5

> Username: pdimov  
> Created at: 2024-04-14 13:11:01 UTC  
> Url: https://github.com/boostorg/crc/issues/23#issuecomment-2054057533  

There is that, yes.  
  
Standalone it is, then. See the current develop branch.

---

## Comment 6

> Username: YarikTH  
> Created at: 2024-04-14 16:19:26 UTC  
> Url: https://github.com/boostorg/crc/issues/23#issuecomment-2054111704  

I made `constexpr` prototyping and I made it work with C++17. `crc_table_t` is left, but `crc_table_t::get_table()` is replaced with `static inline constexpr array_type crc_table_t::table` constant. Both `static inline` for class members and `constexpr` non-const access to `std::array` are C++17 extensions.  
  
`std::array` can be replaced with  
```  
template<typename T, std::size_t N>  
struct my_array  
{  
    T elems[N];  
    // No explicit construct/copy/destroy for aggregate type.  
  
    constexpr std::size_t size() const noexcept { return N; }  
  
    // Element access.  
    constexpr T&  
    operator[]( std::size_t n ) noexcept       { return elems[n]; }  
    constexpr const T&  
    operator[]( std::size_t n ) const noexcept { return elems[n]; }  
};  
```  
for optimization table to make it `constexpr` before C++17, but the main problem is "how to store the table in the constexpr friendly place". Function local static is C++23 extension, class inline static is a C++17 extension. If considering getting rid of table, then C++20 is needed for `std::is_constant_evaluated`.  
  
The other problem is forbidden reinterpret cast from void* to other types. So `process_bytes(const void*, size_t)` and other similar methods should be made (or paired with) templated versions with parameterized ByteType. That can be paired with `<span>` receiving overloads later.  
  
So I think it is enough to provide C++17 `constexpr` support for now.
