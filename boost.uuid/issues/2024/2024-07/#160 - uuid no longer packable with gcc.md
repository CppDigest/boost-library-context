# #160 - uuid no longer packable with gcc [Closed]

> Username: wkelton  
> Created at: 2024-07-26 01:41:31 UTC  
> Updated at: 2025-09-04 00:46:04 UTC  
> Closed at: 2025-09-04 00:46:04 UTC  
> Url: https://github.com/boostorg/uuid/issues/160  

There appears to be a breaking change for uuid in version 1.86 where it cannot be packed in a struct using gcc.  
  
I set up an example showing a 1.85 uuid being packed in a struct, but 1.86 uuid causes a warning from gcc: [Compile Explorer](https://godbolt.org/z/e81naKeYq)  
  
gcc warns:  
```  
warning: ignoring packed attribute because of unpacked non-POD field  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2024-07-26 01:59:58 UTC  
> Url: https://github.com/boostorg/uuid/issues/160#issuecomment-2251826067  

I wasn't able to make `__attribute__((packed))` work reliably after the changes; you'll have to define BOOST_UUID_DISABLE_ALIGNMENT if you need this functionality. (`#pragma pack(1)` works, though.)  
  
The corresponding tests are  
  
https://github.com/boostorg/uuid/blob/02c82cea9c82e4fdbc32b58e5a2e9f97dfc50755/test/test_attribute_packed.cpp  
(which requires BOOST_UUID_DISABLE_ALIGNMENT to pass) and  
  
https://github.com/boostorg/uuid/blob/02c82cea9c82e4fdbc32b58e5a2e9f97dfc50755/test/test_pragma_pack.cpp  
  
which does not.
