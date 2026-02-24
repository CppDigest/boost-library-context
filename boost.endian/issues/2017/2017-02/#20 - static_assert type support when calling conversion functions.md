# #20 - static_assert type support when calling conversion functions [Closed]

> Username: norbertwenzel  
> Created at: 2017-02-12 08:53:52 UTC  
> Updated at: 2019-04-29 16:31:57 UTC  
> Closed at: 2019-04-29 16:31:57 UTC  
> Url: https://github.com/boostorg/endian/issues/20  

The docs clearly state there is no support for floating point type endian conversion. Nevertheless the following code compiles fine on my x86_64 (ie. little endian) computer:  
```cpp  
float f = 3.1415f;  
double d = 2.7182;  
boost::endian::native_to_little(f);  
boost::endian::native_to_little(d);  
```  
Since no conversion needs to be done everything is fine. When this code is changed to `native_to_big()` (or I assume compiled on a big endian system) the compiler starts complaining about ambiguous overloads:  
```  
error: call of overloaded ‘endian_reverse(float&)’ is ambiguous  
```  
I understand this is documented but as a C++ programmer I unfortunately have developed the habit that everything is fine as long as the compiler does not bark. So I would prefer a `static_assert` that always checks if the conversion is supported, regardless of the conversion being actually necessary on the current platform.  
  
I'd be willing to try writing this check but I wanted to ask if such a check would be of interest first?

---

## Comment 1

> Username: arvidn  
> Created at: 2017-11-04 11:54:15 UTC  
> Url: https://github.com/boostorg/endian/issues/20#issuecomment-341891048  

an alternative approach to a would be to add ``= delete`` overloads.

---

## Comment 2

> Username: pdimov  
> Created at: 2019-04-29 16:31:57 UTC  
> Url: https://github.com/boostorg/endian/issues/20#issuecomment-487650023  

The develop branch now static-asserts on `native_to_little(float)`.
