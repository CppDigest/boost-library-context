# #85 - `BOOST_URL_ERR` with source location [Closed]

> Username: alandefreitas  
> Created at: 2021-11-23 23:26:41 UTC  
> Updated at: 2021-11-23 23:40:45 UTC  
> Closed at: 2021-11-23 23:40:38 UTC  
> Url: https://github.com/boostorg/url/issues/85  

When compiling with CMake, `config.hpp` has `BOOST_URL_ERR`:  
  
```cpp  
# define BOOST_URL_ERR(ev) (::boost::system::error_code( (ev), [] { \  
         static constexpr auto loc(BOOST_CURRENT_LOCATION); \  
         return &loc; }()))  
```  
  
In `parse.hpp`, we have:  
  
```cpp  
ec = BOOST_URL_ERR(error::incomplete);  
```  
  
so it expands to:  
  
```cpp  
ec = ::boost::system::error_code( (error::incomplete), [] {  
         static constexpr auto loc(BOOST_CURRENT_LOCATION);  
         return &loc; }());  
```  
  
which gives:  
  
```  
boost/libs/url/include/boost/url/bnf/impl/parse.hpp:29:14: error: no matching constructor for initialization of '::boost::system::error_code'  
        ec = ::boost::system::error_code( (error::incomplete), [] {  
             ^                            ~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
The macro gives the error_code two arguments but the only constructor that receives a `source_location` is `error_code( int val, const error_category & cat, source_location const * loc )`. The errors for each constructor are:  
  
- Expects single parameter: `candidate constructor [with ErrorCodeEnum = boost::urls::error] not viable: no known conversion from 'const boost::source_location *' to 'typename detail::enable_if<is_error_code_enum<error>::value>::type *' (aka 'void *') for 2nd argument`  
- Expects int: `candidate constructor not viable: no known conversion from 'boost::urls::error' to 'int' for 1st argument`  
- Expects single std::error_code: `candidate constructor not viable: requires single argument 'ec', but 2 arguments were provided`  
- Expects single boost::error_code: `candidate constructor (the implicit copy constructor) not viable: requires 1 argument, but 2 were provided`  
- Expects single boost::error_code: `candidate constructor (the implicit move constructor) not viable: requires 1 argument, but 2 were provided`  
- Expects 3 arguments. The only one that expects a source_location and should work if we add the category. `candidate constructor not viable: requires 3 arguments, but 2 were provided`  
- Expects no arguments: `candidate constructor not viable: requires 0 arguments, but 2 were provided`  
  
The simpler macro:  
  
```cpp  
#ifdef BOOST_URL_NO_SOURCE_LOCATION  
# define BOOST_URL_ERR(ev) (ev)  
#else  
```  
  
works as expected.  
  
Also related to that, the test functions in `test/unit/error.cpp` expects `error`s to be an error_code, but the calling function gives them an `enum`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-11-23 23:36:10 UTC  
> Url: https://github.com/boostorg/url/issues/85#issuecomment-977283167  

I need to push my changes up

---

## Comment 2

> Username: pdimov  
> Created at: 2021-11-23 23:36:18 UTC  
> Url: https://github.com/boostorg/url/issues/85#issuecomment-977283249  

There is a two-argument constructor taking source location, added in https://github.com/boostorg/system/commit/0ccf08509b6bbb415ecff9a266d34d77b71cd253. You probably need to update your Boost.System submodule.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2021-11-23 23:40:38 UTC  
> Updated at: 2021-11-23 23:40:45 UTC  
> Url: https://github.com/boostorg/url/issues/85#issuecomment-977286041  

Oh...  OK. Thanks!
