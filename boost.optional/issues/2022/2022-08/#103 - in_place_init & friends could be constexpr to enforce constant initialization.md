# #103 - in_place_init & friends could be constexpr to enforce constant initialization [Closed]

> Username: ldionne  
> Created at: 2022-08-08 21:25:24 UTC  
> Updated at: 2026-02-18 20:25:31 UTC  
> Closed at: 2026-02-18 20:25:31 UTC  
> Url: https://github.com/boostorg/optional/issues/103  

In https://github.com/boostorg/optional/blob/develop/include/boost/optional/optional.hpp#L93-L106, we have:  
  
```  
struct in_place_init_t  
{  
  struct init_tag{};  
  explicit in_place_init_t(init_tag){}  
};  
const in_place_init_t in_place_init ((in_place_init_t::init_tag()));  
  
struct in_place_init_if_t  
{  
  struct init_tag{};  
  explicit in_place_init_if_t(init_tag){}  
};  
const in_place_init_if_t in_place_init_if ((in_place_init_if_t::init_tag()));  
```  
  
This results in static initializers in binaries (although trivial ones). Instead, it should be valid to change their definition to the following, which would definitely get rid of static initializers in >= C++11:  
  
```  
struct in_place_init_t  
{  
  struct init_tag{};  
  BOOST_CONSTEXPR explicit in_place_init_t(init_tag){}  
};  
BOOST_CONSTEXPR_OR_CONST in_place_init_t in_place_init ((in_place_init_t::init_tag()));  
  
struct in_place_init_if_t  
{  
  struct init_tag{};  
  BOOST_CONSTEXPR explicit in_place_init_if_t(init_tag){}  
};  
BOOST_CONSTEXPR_OR_CONST in_place_init_if_t in_place_init_if ((in_place_init_if_t::init_tag()));  
```

---

## Comment 1

> Username: andry81  
> Created at: 2022-08-09 00:19:37 UTC  
> Updated at: 2022-08-09 00:20:41 UTC  
> Url: https://github.com/boostorg/optional/issues/103#issuecomment-1208751969  

Beside of the question, can someone explain why the `BOOST_CONSTEXPR_OR_CONST` and others does even exist? Why not just use `BOOST_CONSTEXPR` with `const` or `static` explicitly? The `constexpr` if apply, then already implies `const` because of const-ness of entire thing. Why to replace it to something w/o `const` even if `constexpr` could not apply? This `boost` implementation part looks quite stupid.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-09-13 14:23:34 UTC  
> Url: https://github.com/boostorg/optional/issues/103#issuecomment-2349084996  

Implemented in develop.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2026-02-18 20:25:31 UTC  
> Url: https://github.com/boostorg/optional/issues/103#issuecomment-3923031090  

This is now merged to master.
