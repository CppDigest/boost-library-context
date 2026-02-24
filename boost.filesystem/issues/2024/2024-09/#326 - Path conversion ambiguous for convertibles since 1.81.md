# #326 - Path conversion ambiguous for convertibles since 1.81 [Closed]

> Username: Flamefire  
> Created at: 2024-09-29 18:38:56 UTC  
> Updated at: 2024-10-01 09:01:18 UTC  
> Closed at: 2024-09-30 00:42:58 UTC  
> Url: https://github.com/boostorg/filesystem/issues/326  

I have a type that has custom conversion operators to string and path. With b219d9fb8af760b54b014d1223ebd5d43b4d07b5 this got ambiguous:  
  
```  
struct X  
{  
    operator const char*() const;  
    operator std::string() const;  
    operator boost::filesystem::path() const;  
};  
boost::filesystem::path p = X();  
```  
  
> boost/filesystem/detail/path_traits.hpp(482,21): message : kann "boost::filesystem::detail::path_traits::yes_type boost::filesystem::detail::path_traits::is_convertible_to_path_source<X>::_check_convertible_to_path_source(const std::string &)" sein  
> boost/filesystem/detail/path_traits.hpp(480,21): message : oder "boost::filesystem::detail::path_traits::yes_type boost::filesystem::detail::path_traits::is_convertible_to_path_source<X>::_check_convertible_to_path_source(const char *)"

---

## Comment 1

> Username: Lastique  
> Created at: 2024-09-29 22:17:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/326#issuecomment-2381631235  

I can probably disable the member template instantiations if the argument is convertible to `path` (so that the non-template members are used), and this should fix your test case. But if your type is convertible to multiple path source types but not `path` then the call will remain ambiguous. And it indeed should be ambiguous as the library should not decide which conversion path to use.

---

## Comment 2

> Username: Flamefire  
> Created at: 2024-09-30 07:19:25 UTC  
> Url: https://github.com/boostorg/filesystem/issues/326#issuecomment-2382303794  

Thanks for the fix! I'd suggest to enhance the test case to cover all possible conversion sources that are used in the trait:  
  
- `const std::wstring_view &`  
- `const std::string_view &`  
- `const std::wstring &`  
- `const std::string &`  
- `const wchar_t *`  
- `const char *`  
  
I.e. it not only checks for the "native char" type. Not sure that was even intentional. If not I'd suggest a test case with a class convertible to the narrow and wide char stringish-types, i.e.   
  
```  
struct Convertible_to_CStrings {  
  operator const char* () const;  
  operator const wchar_t* () const;  
};  
struct Convertible_to_Strings {  
  operator const std::string& () const;  
  operator const std::wstring& () const;  
};  
```  
  
I think for the non-native strings the utf8-converter is used, but what if the source class already implements `narrow<->wide` conversion? IMO the members should then prefer the native char type and allow the above classes.

---

## Comment 3

> Username: Lastique  
> Created at: 2024-09-30 08:35:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/326#issuecomment-2382460607  

On September 30, 2024 10:19:47 AM Alexander Grund   
***@***.***> wrote:  
  
> If not I'd suggest a test case with a class convertible to the narrow and   
> wide char stringish-types, i.e.  
>  
> ```  
> struct Convertible_to_CStrings {  
>  operator const char* () const;  
>  operator const wchar_t* () const;  
> };  
> struct Convertible_to_Strings {  
>  operator const std::string& () const;  
>  operator const std::wstring& () const;  
> };  
> ```  
  
As I said earlier, this will not work, and this is intentional. The library   
should not choose a conversion path for the user. Choosing ‘path‘ as the   
default is already dubious enough, but it at least makes sense.  
  
  
In my opinion, the original example is poor design, and the conversion   
operators should probably be reconsidered.  
  
> I think for the non-native strings the utf8-converter is used, but what if   
> the source class already implements `narrow<->wide` conversion? IMO the   
> members should then prefer the native char type and allow the above classes.  
  
No, the library should not prefer anything. The user should decide and   
apply explicit type casts. Or better yet, not use type conversion operators   
for anything other than expressing the relation "A is B". Anything more   
elaborate than that is better expressed with named functions.

---

## Comment 4

> Username: Flamefire  
> Created at: 2024-10-01 09:01:17 UTC  
> Url: https://github.com/boostorg/filesystem/issues/326#issuecomment-2385216602  

> As I said earlier, this will not work, and this is intentional.   
  
I added a PR to enhance the test that in such cases a `path` conversion operator still works: https://github.com/boostorg/filesystem/pull/327  
  
> In my opinion, the original example is poor design, and the conversion operators should probably be reconsidered.  
  
The example is very reduced. My real example uses some kind of `variant/any` which is implicitly convertible although it may throw if the wrong target type is used. The generic code then uses something like `template <class T> get(any& value){ /*validation code */ return value; }`
