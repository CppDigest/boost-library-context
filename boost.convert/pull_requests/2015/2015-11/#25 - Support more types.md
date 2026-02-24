# #25 Support more types [Closed]

> Username: res2k  
> Created at: 2015-11-21 14:02:33 UTC  
> Updated at: 2015-11-22 11:27:52 UTC  
> Closed at: 2015-11-22 03:57:17 UTC  
> Url: https://github.com/boostorg/convert/pull/25  

These changes add support for more conversion constellations:  
- Allow spirit to write to wide (actually, any) strings  
- Make strtol converter support converting unsigned types to strings  
- Make spirit and strtol support 'long long' types

---

## Comment 1

> Username: yet-another-user  
> Created_at: 2015-11-22 03:57:17 UTC  
> Updated_at: 2015-11-22 03:57:51 UTC  
> Url: https://github.com/boostorg/convert/pull/25#issuecomment-158706084  

Frank, Thank you for your contribution. It's much and truly appreciated. I merged it all except ui_to_str(). It seems unnecessary as its functionality is covered by the existing i_to_str(). All tests ran successfully. Please correct me if I missed anything. Thanks again. Vladimir.

---

## Comment 2

> Username: res2k  
> Created_at: 2015-11-22 11:06:01 UTC  
> Url: https://github.com/boostorg/convert/pull/25#issuecomment-158746212  

Well, the motivation behind ui_to_str() was to avoid (possible) compiler warnings when negating an unsigned value. At least MSVC warns against that (though the particular one is among those disabled via #pragma).

---

## Comment 3

> Username: res2k  
> Created_at: 2015-11-22 11:09:03 UTC  
> Url: https://github.com/boostorg/convert/pull/25#issuecomment-158746477  

And, _facepalm_, I just noticed something else: I got some types wrong in strtol test_int_to_str()...

---
