# #1863 - Hash collision on big endian systems in http::detail::field_table [Closed]

> Username: oliverdaniell  
> Created at: 2020-02-28 15:50:44 UTC  
> Updated at: 2020-03-02 08:24:24 UTC  
> Closed at: 2020-02-29 22:32:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1863  

Boost version 1.70.0  
  
On big endian systems there is a hash collision between the  `Autosubmitted` and `Distribution` fields in http::detail::field_table. The difference in behaviour with little endian systems is caused by reinterpreting the string as an integer at `boost/beast/http/impl/field.ipp` on lines 42, 74 and 75. I believe this can be fixed using   
  
```  
endian::native_to_little_inplace(v);  
```  
  
You can reproduce the issue using the following code  
  
```  
#include <boost/beast/http/field.hpp>  
int main() { boost::beast::http::detail::field_table table; }  
```  
  
Output:  
  
```  
boost/beast/http/impl/field.ipp:457: boost::beast::http::detail::field_table::field_table(): Assertion map_[j][0] == 0' failed.  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-02-28 15:52:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1863#issuecomment-592573893  

but.. a hash collision should be okay since it compares to make sure there's a match and then moves on to then next entry?

---

## Comment 2

> Username: oliverdaniell  
> Created at: 2020-02-28 15:54:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1863#issuecomment-592575016  

This assertion fires during construction of field_table  
```  
457: BOOST_ASSERT(map_[j][0] == 0);  
```  
  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/http/impl/field.ipp

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-02-28 16:00:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1863#issuecomment-592577348  

hmm it looks like the invariant is that there are no collisions for field numbers 0..255 (which get put into `map_[][0]` and then there must be no collisions for field numbers 256+ (which go to `map_[][1]`) does this sound right?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-02-28 16:04:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1863#issuecomment-592579179  

Can you please submit a pull request, but instead of using `endian::native_to_little_inplace` just put an `inline` function into field.ipp that does the same thing?

---

## Comment 5

> Username: oliverdaniell  
> Created at: 2020-02-28 16:06:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1863#issuecomment-592580278  

> hmm it looks like the invariant is that there are no collisions for field numbers 0..255 (which get put into map_[][0] and then there must be no collisions for field numbers 256+ (which go to map_[][1]) does this sound right?  
  
Yes the hash for the first 256 fields must be unique in order to perform the reverse lookup on line 480

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-02-28 16:09:50 UTC  
> Updated at: 2020-02-28 16:11:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1863#issuecomment-592581852  

Instead of `memcpy` would something like this work  
```  
            v =  p[0] |  
                (p[1] <<  8) |  
                (p[2] << 16) |  
                (p[3] << 24);  
```

---

## Comment 7

> Username: oliverdaniell  
> Created at: 2020-02-28 16:13:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1863#issuecomment-592583314  

I believe so, I'll add an inline function as the same behaviour is required for lines 74,75

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-02-28 16:17:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1863#issuecomment-592585176  

Don't worry about the pull request, I am handling it. I realize that there should have been more comments in these functions, because I can barely recognize what it is doing! I will have something for you shortly.

---

## Comment 9

> Username: oliverdaniell  
> Created at: 2020-02-28 16:22:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1863#issuecomment-592587696  

```  
    static std::uint32_t little_to_native(unsigned char const* p)  
    {  
        return  p[0] |  
                (p[1] <<  8) |  
                (p[2] << 16) |  
                (p[3] << 24);  
    }  
```  
```  
std::uint32_t v = little_to_native(p);  
```  
  
```  
Int v1 = little_to_native(reinterpret_cast<unsigned char const*>(p1));  
Int v2 = little_to_native(reinterpret_cast<unsigned char const*>(p2));  
```  
  
I've added a quick test to my powerpc platform and it looks like this does the job. I'm happy to test the merge request when it's available.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2020-02-28 16:49:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1863#issuecomment-592599284  

Here it is https://github.com/boostorg/beast/pull/1864

---

## Comment 11

> Username: oliverdaniell  
> Created at: 2020-03-02 08:24:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1863#issuecomment-593278715  

Merge request looks good, thanks for the help.
