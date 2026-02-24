# #17 Port to Windows Runtime [Closed]

> Username: john-peterson  
> Created at: 2015-03-10 04:12:01 UTC  
> Updated at: 2015-03-14 17:31:04 UTC  
> Closed at: 2015-03-14 17:31:04 UTC  
> Url: https://github.com/boostorg/random/pull/17  

CryptGenRandom isn't in WinRT https://github.com/mirror/boost/issues/5#issuecomment-77984515

---

## Comment 1

> Username: swatanabe  
> Created_at: 2015-03-10 04:47:35 UTC  
> Url: https://github.com/boostorg/random/pull/17#issuecomment-77995397  

AMDG  
- There's no reason to use vector.  An array should  
  work just fine, since the size is known at compile  
  time  
- Is there a reason to go through DataReader instead of  
  just calling CryptographicBuffer::CopyToByteArray?  
- I'd prefer not to use auto.  Boost.Random is still  
  C++03 compatible.  
- When was WINAPI_FAMILY_PARTITION defined?  I don't  
  want to break compatibility with older compilers.  
  I still test with VC9.  
- Similarly, will it work with MinGW or clang-cl?  
- Where are these components coming from?  I notice  
  that you #ifdefed out the #include <windows.h>.  
- Can these functions fail?  What happens if they do?  
  The MSDN documentation is nearly useless.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: john-peterson  
> Created_at: 2015-03-10 05:20:36 UTC  
> Updated_at: 2015-03-10 05:26:10 UTC  
> Url: https://github.com/boostorg/random/pull/17#issuecomment-77997594  

> There's no reason to use vector.   
> Is there a reason to go through DataReader  
  
i fixed it  
  
> When was WINAPI_FAMILY_PARTITION defined?   
  
i fixed the config to the same as https://github.com/boostorg/asio/blob/master/include/boost/asio/detail/config.hpp so it should work  
  
> I'd prefer not to use auto.   
  
there's no winrt compiler that doesn't support auto  
  
> will it work with MinGW or clang-cl?  
  
do they have an rt compiler?  
  
> Where are these components coming from?  
  
the namespaces comes from /ZW  
  
> Can these functions fail?   
  
doesn't seem so  
  
**edit:** maybe they can throw an exception? should they be in a try/catch?

---

## Comment 3

> Username: swatanabe  
> Created_at: 2015-03-10 16:27:56 UTC  
> Url: https://github.com/boostorg/random/pull/17#issuecomment-78090734  

AMDG  
  
On 03/09/2015 11:20 PM, John Sebastian Peterson wrote:  
  
> > I'd prefer not to use auto.   
>   
> there's no winrt compiler that doesn't support auto  
  
Okay.  
  
> > will it work with MinGW or clang-cl?  
>   
> do they have an rt compiler?  
  
My concern wasn't so much compiling for WinRT  
as the use of an unknown macro breaking desktop  
compilation.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: john-peterson  
> Created_at: 2015-03-10 16:29:26 UTC  
> Updated_at: 2015-03-10 16:43:31 UTC  
> Url: https://github.com/boostorg/random/pull/17#issuecomment-78091088  

k i get it

---

## Comment 5

> Username: swatanabe  
> Created_at: 2015-03-11 21:59:36 UTC  
> Url: https://github.com/boostorg/random/pull/17#issuecomment-78381757  

AMDG  
  
By array I meant unsigned char[sizeof(result)].  
Also, I'd rather not put any kind of WinRT  
configuration in the header when it's only  
used by random_device.cpp.  
  
In Christ,  
Steven Watanabe

---

## Comment 6

> Username: john-peterson  
> Created_at: 2015-03-11 23:11:21 UTC  
> Url: https://github.com/boostorg/random/pull/17#issuecomment-78393007  

done  
  
> By array I meant unsigned char[sizeof(result)].  
  
it doesn't accept that type  
  
> https://msdn.microsoft.com/en-us/library/windows.security.cryptography.cryptographicbuffer.copytobytearray.aspx  
>   
> Type: array of Number [JavaScript] | System.Byte[] [.NET] | Platform::Array<uint8> [C++]

---

## Comment 7

> Username: swatanabe  
> Created_at: 2015-03-13 20:45:30 UTC  
> Url: https://github.com/boostorg/random/pull/17#issuecomment-79371881  

AMDG  
  
The most recent version of the WinRT #ifdefing  
is broken:  
  
compile-c-c++  
......\bin.v2\libs\random\build\msvc-12.0\debug\threading-multi\random_device.obj  
random_device.cpp  
......\libs\random\src\random_device.cpp(117) : error C2653:  
'CryptographicBuffer' : is not a class or namespace name  
  
In Christ,  
Steven Watanabe

---

## Comment 8

> Username: john-peterson  
> Created_at: 2015-03-13 22:03:15 UTC  
> Url: https://github.com/boostorg/random/pull/17#issuecomment-79461447  

u need to add /ZW https://github.com/boostorg/build/pull/62

---

## Comment 9

> Username: swatanabe  
> Created_at: 2015-03-13 22:12:27 UTC  
> Url: https://github.com/boostorg/random/pull/17#issuecomment-79467526  

AMDG  
  
On 03/13/2015 04:03 PM, John Sebastian Peterson wrote:  
  
> u need to add /ZW https://github.com/boostorg/build/pull/62  
  
I'm _NOT_ building for WinRT.  
  
In Christ,  
Steven Watanabe

---

## Comment 10

> Username: john-peterson  
> Created_at: 2015-03-13 22:23:50 UTC  
> Url: https://github.com/boostorg/random/pull/17#issuecomment-79482181  

k so winapifamily.h is included for all vc 2013 builds then https://github.com/jp9000/OBS/blob/master/extras/winapifamily.h#L34

---

## Comment 11

> Username: swatanabe  
> Created_at: 2015-03-14 17:31:04 UTC  
> Url: https://github.com/boostorg/random/pull/17#issuecomment-80619060  

I've rebased this and applied it to develop.

---
