# #52 Refactored random generator handling inside Boost.Uuid [Closed]

> Username: jeking3  
> Created at: 2017-11-06 22:22:05 UTC  
> Updated at: 2018-04-29 14:00:32 UTC  
> Closed at: 2017-11-07 04:07:00 UTC  
> Url: https://github.com/boostorg/uuid/pull/52  

Changed the default random_generator implementation to use  
operating-system provided entropy as it is more secure and  
faster for the typical use case of generating one uuid at  
a time.  
  
This is a breaking change for anyone passing a mt19937  
into one of the explicit constructors of random_generator,  
which would be quite rare.  
  
Changed the default random provider on Windows to use BCrypt  
where available, falling back to Wincrypt when necessary or  
when explicitly requested through a macro.  
  
Provide a new random_generator_mt19937 type definition for  
use cases where a large number of uuids need to be created  
with high performance.  This is equivalent to the previous  
definition of random_generator.  
  
Provide a random generation benchmark test showing the  
cutoff where the bulk generator will outperform the  
standard generator based on wall time.  
  
Removed template specialization for boost::random::random_device  
so that any UniformRandomNumberGenerator can be used properly  
with random_generator.  
  
Replaced the seed_rng detail implementation (which had a number of  
flaws) with a replacement header-only random_device implementation.  
  
Note: entropy generation errors will cause an entropy_error  
to be thrown from random_generator.  The previous implementation  
ignored errors and silently failed.  
  
Added support for entropy generation on cloudabi platform  
leveraging the new random_device implementation.  
  
Added support for Universal Windows Platform (UWP) development  
leveraging the new random_device implementation.  
  
This fixes #24

---

## Comment 1

> Username: swatanabe  
> Created_at: 2017-11-06 22:59:26 UTC  
> Url: https://github.com/boostorg/uuid/pull/52#issuecomment-342316922  

AMDG  
  
Your refactoring of basic_random_generator to avoid unifom_int  
is very wrong.  
  
- A uniform random number generator is not guaranteed to  
  fill all the bits of its result_type.  The range is not  
  even guaranteed to be a power of 2.  Think linear congruential.  
  There are also several generators that have 48-bit outputs  
  stored in a 64-bit type.  This is not even considering the  
  fact that the standard doesn't guarantee that there is an  
  integer type with exactly 32 bits, which is why the standard  
  engines are all specified in terms of uint_leastNN_t instead  
  of uintNN_t.  
- Using memcpy means that the output is endian dependent  
  (Using mt19937 which is explicitly seeded to a fixed value  
  by the user can now give platform dependent results)  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-11-06 23:32:00 UTC  
> Url: https://github.com/boostorg/uuid/pull/52#issuecomment-342323809  

Yeah, what Steven said.  
  
In addition:  
  
- as I already stated, your random_device is overcomplicated and overtemplatized. The correct interface for obtaining entropy from the OS is  
  
```  
int get_random_bytes( void* p, size_t n );  
```  
  
which you can implement in its various flavors (`get_random_bytes_bcrypt` and so on) and then call the appropriate one using either a macro or an inline function.  
  
If you prefer to have the state (f.ex. `hProv_`) encapsulated in a class instead, there's still no need to fit the entropy provider into the URNG interface. This has forced you into questionable optimizations only because you generate random bytes at one end, try to pass them through an URNG pipe, then at the other end try to recover the random bytes from the URNG, breaking actual URNG use in the process.  
  
Just make the implementation detail device produce bytes, take these bytes in `random_generator`, and leave `basic_name_generator<URNG>` alone. There is no need to pipe things through the wrong concept.  
  
- when autolinking, you should honor `BOOST_ALL_NO_LIB` and should `#undef BOOST_AUTO_LINK_NOMANGLE` afterwards. `auto_link.hpp` will `#undef BOOST_LIB_NAME` for you, but not the other one.  
  
- as a meta observation, I'd prefer the documentation to not go into detail about the specific cutoffs at which `_mt19937` outperforms. Programmers are notoriously prone to prefer performance over correctness or security and this will further encourage them away from the secure option (as if they need any encouragement).

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2017-11-07 02:19:50 UTC  
> Url: https://github.com/boostorg/uuid/pull/52#issuecomment-342354458  

# [Codecov](https://codecov.io/gh/boostorg/uuid/pull/52?src=pr&el=h1) Report  
> Merging [#52](https://codecov.io/gh/boostorg/uuid/pull/52?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/uuid/commit/5a48cab9af5b522c6f4851b7312761493e2e4c78?src=pr&el=desc) will **decrease** coverage by `1.74%`.  
> The diff coverage is `80%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/uuid/pull/52/graphs/tree.svg?token=6falIr5npV&src=pr&width=650&height=150)](https://codecov.io/gh/boostorg/uuid/pull/52?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #52      +/-   ##  
===========================================  
- Coverage    97.41%   95.67%   -1.75%       
===========================================  
  Files           11       14       +3       
  Lines          619      601      -18       
===========================================  
- Hits           603      575      -28       
- Misses          16       26      +10  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/uuid/pull/52?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/uuid/entropy\_error.hpp](https://codecov.io/gh/boostorg/uuid/pull/52?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2VudHJvcHlfZXJyb3IuaHBw) | `0% <0%> (ø)` | |  
| [include/boost/uuid/detail/random/random\_device.hpp](https://codecov.io/gh/boostorg/uuid/pull/52?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC9yYW5kb20vcmFuZG9tX2RldmljZS5ocHA=) | `100% <100%> (ø)` | |  
| [...de/boost/uuid/detail/random/random\_device\_file.hpp](https://codecov.io/gh/boostorg/uuid/pull/52?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC9yYW5kb20vcmFuZG9tX2RldmljZV9maWxlLmhwcA==) | `84.21% <84.21%> (ø)` | |  
| [include/boost/uuid/random\_generator.hpp](https://codecov.io/gh/boostorg/uuid/pull/52?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL3JhbmRvbV9nZW5lcmF0b3IuaHBw) | `96.87% <94.44%> (-3.13%)` | :arrow_down: |  
| [include/boost/uuid/uuid\_serialize.hpp](https://codecov.io/gh/boostorg/uuid/pull/52?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL3V1aWRfc2VyaWFsaXplLmhwcA==) | `100% <0%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/uuid/pull/52?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/uuid/pull/52?src=pr&el=footer). Last update [5a48cab...f22c7d9](https://codecov.io/gh/boostorg/uuid/pull/52?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: jeking3  
> Created_at: 2017-11-07 04:07:00 UTC  
> Url: https://github.com/boostorg/uuid/pull/52#issuecomment-342369834  

Thanks for the feedback, I'm working on the suggested changes.

---

## Comment 5

> Username: Lastique  
> Created_at: 2017-11-07 15:53:59 UTC  
> Url: https://github.com/boostorg/uuid/pull/52#issuecomment-342527188  

I can add a few comments:  
  
- Never use `errno` in expressions, such as exception constructor calls. It is unspecified at which point `errno` value is read, it is possible that its value has been clobbered by other subexpressions, such as constructor arguments construction or `BOOST_THROW_EXCEPTION` implementation. Read `errno` explicitly, as the first expression inside `if`.  
  
- /dev/urandom is not universally available, although widespread. From [here](https://en.wikipedia.org/wiki//dev/random), it is not available on OpenBSD. You can use /dev/random where /dev/urandom is not available.  
  
- For all calls to `open`, please add `O_CLOEXEC`, if one is defined, to avoid possible file descriptor leaks.

---

## Comment 6

> Username: Lastique  
> Created_at: 2017-11-07 15:55:53 UTC  
> Url: https://github.com/boostorg/uuid/pull/52#issuecomment-342527884  

Also, support for `getrandom` and `getentropy` would be welcome, but can be added later.

---

## Comment 7

> Username: pdimov  
> Created_at: 2017-11-07 16:25:06 UTC  
> Url: https://github.com/boostorg/uuid/pull/52#issuecomment-342537767  

On OpenBSD it would probably be best to call `arc4random_buf` directly instead of reading `/dev/random`, the latter doing the same thing in a more inefficient manner.

---

## Comment 8

> Username: jeking3  
> Created_at: 2017-11-07 17:48:39 UTC  
> Url: https://github.com/boostorg/uuid/pull/52#issuecomment-342564946  

I have already added support for getentropy if glibc is >= 2.25; I wanted to add support on OpenBSD however Boost.Predef only goes up to 4.9 and I need to be able to detect 5.6, so I opened https://github.com/boostorg/predef/issues/66 for it.  
  
I got rid of all the templatization you asked and have a very, very simple implementation now which I hope you will like more.  The basic_random_generator is back to the same thing it was before, but with the added detection of whether the passed-in URNG has a seed() method that needs to be called (i.e. it is a PRNG).  
  
It was my understanding that arc4random_buf is a PRNG and it should be avoided.  
  
I will add support to try urandom first, then random, then fail; unless BOOST_UUID_RANDOM_PROIVIDER_POSIX_BLOCKING is defined, in which case we will only try to open /dev/random.  I will take a look at the errno and open flags issues.  
  
When a new PR is ready I will add the link here so you will all be notified.  I changed the name of the random provider to random_provider, instead of random_device, to avoid confusion with Boost.Random, and it's still in uuids::detail but not in uuids::detail::random any more.  
  
There is a random_provider with one method, the get_random_bytes that Peter asked for.  This is implemented for CloudABI, BCrypt, Wincrypt, getentropy, and POSIX each in a separate header.  I did NOT make it a functor, although I could have, out of concerns it would be rejected as more complex than what was asked, so I made a separate random_functor<Type> which returns <Type> initialized with data from random_provider.  I still need functor behavior in order to properly seed a PRNG passed into basic_random_generator.  
  
Well, you'll see in the PR.  It's an improvement.  
  
Thanks.

---

## Comment 9

> Username: pdimov  
> Created_at: 2017-11-07 18:05:43 UTC  
> Url: https://github.com/boostorg/uuid/pull/52#issuecomment-342570129  

`arc4random_buf` is what reading from `/dev/random` returns on OpenBSD as far as I know.  
  
https://man.openbsd.org/arc4random.3  
https://man.openbsd.org/random.4  
  
As far as "arc4random_buf is a PRNG", they are all PRNGs. The important thing is that it's a CSPRNG that is periodically seeded by hardware entropy.  
  
That last man page says that there _is_ `/dev/urandom` on OpenBSD, by the way. I would've been surprised if there weren't.  
  
> The random and urandom devices have been available since OpenBSD 2.0.  
  
> Never use /dev/random.  
  
Yeah, I agree. :-)

---

## Comment 10

> Username: pdimov  
> Created_at: 2017-11-07 18:07:27 UTC  
> Updated_at: 2017-11-07 18:08:34 UTC  
> Url: https://github.com/boostorg/uuid/pull/52#issuecomment-342570597  

Also note  
  
> The urandom device is intended to be used in scripts. In C programs, use the arc4random(3) family of functions instead  
  
and  
  
> These functions are always successful, and no return value is reserved to indicate an error.  
  
and  
  
> These functions first appeared in OpenBSD 2.1.

---

## Comment 11

> Username: swatanabe  
> Created_at: 2017-11-07 18:07:31 UTC  
> Url: https://github.com/boostorg/uuid/pull/52#issuecomment-342570621  

AMDG  
  
On 11/07/2017 10:48 AM, James E. King, III wrote:  
>   
> It was my understanding that arc4random_buf is a PRNG and it should be avoided.  
>   
  
  arc4random uses a cryptographic PRNG, which is  
fine and the same as /dev/urandom, RtlGenRandom, etc.  
  
> <snip>  
> There is a random_provider with one method, the get_random_bytes that Peter asked for.  This is implemented for CloudABI, BCrypt, Wincrypt, getentropy, and POSIX each in a separate header.  I did NOT make it a functor, although I could have, out of concerns it would be rejected as more complex than what was asked, so I made a separate random_functor<Type> which returns <Type> initialized with data from random_provider.  I still need functor behavior in order to properly seed a PRNG passed into basic_random_generator.  
>   
  
  You should probably use the SeedSeq version of seed,  
which will allow it to work with std::random, and also  
allows setting the whole state in a single call.  
  
In Christ,  
Steven Watanabe

---

## Comment 12

> Username: pdimov  
> Created_at: 2017-11-07 18:20:50 UTC  
> Updated_at: 2017-11-07 18:21:27 UTC  
> Url: https://github.com/boostorg/uuid/pull/52#issuecomment-342574511  

Good point. It seems to me that if you add the `template<class RI> void generate( RI first, RI last );` method to `random_provider`, it will meet the requirements of `SeedSequence` and there'll be no need for `random_functor` or `generator_iterator`.  
  
I think that we can be excused to assume no padding bits or trap representations in unsigned integer types. :-)  
  
http://www.boost.org/doc/libs/1_65_1/doc/html/boost_random/reference.html#boost_random.reference.concepts.seed_sequence

---

## Comment 13

> Username: swatanabe  
> Created_at: 2017-11-07 19:41:29 UTC  
> Url: https://github.com/boostorg/uuid/pull/52#issuecomment-342598470  

AMDG  
  
On 11/07/2017 11:20 AM, Peter Dimov wrote:  
> Good point. It seems to me that if you add the `template<class RI> void generate( RI first, RI last );` method to `random_provider`, it will meet the requirements of `SeedSequence` and there'll be no need for `random_functor` or `generator_iterator`.  
>   
> I think that we can be excused to assume no padding bits or trap representations in unsigned integer types. :-)  
>   
  
  The requirement of RI is that its value_type must  
be an unsigned integer type capable of representing  
values in [0, 2^32).  In practice, RI is almost always  
going to be uint_least32_t*, but it is permitted to point  
to a wider type, and it isn't guaranteed to be a raw pointer  
or to point to contiguous storage.  
  
In Christ,  
Steven Watanabe

---

## Comment 14

> Username: pdimov  
> Created_at: 2017-11-07 20:01:51 UTC  
> Url: https://github.com/boostorg/uuid/pull/52#issuecomment-342604361  

Yes. The question is whether we can just generate the random bytes into `*it` directly, not fill the whole range in one call. It's not quite clear to me when `*it` is `uint64_t` whether `generate` is still supposed to put an `uint32_t` into it, or it can put a random `uint64_t` there. If the former, we need to `&= 0xFFFFFFFFu` it.

---

## Comment 15

> Username: swatanabe  
> Created_at: 2017-11-07 20:09:19 UTC  
> Url: https://github.com/boostorg/uuid/pull/52#issuecomment-342606542  

AMDG  
  
On 11/07/2017 01:01 PM, Peter Dimov wrote:  
> Yes. The question is whether we can just generate the random bytes into `*it` directly, not fill the whole range in one call. It's not quite clear to me when `*it` is `uint64_t` whether `generate` is still supposed to put an `uint32_t` into it, or it can put a random `uint64_t` there. If the former, we need to `&= 0xFFFFFFFFu` it.  
>   
  
rand.req.seedseq:  
q.generate(rb,re) - "Does nothing if rb == re.  
Otherwise, fills the supplied sequence [rb, re)  
with 32-bit quantities..."  
  
In Christ,  
Steven Watatanabe

---

## Comment 16

> Username: pdimov  
> Created_at: 2017-11-07 20:18:38 UTC  
> Updated_at: 2017-11-07 20:20:30 UTC  
> Url: https://github.com/boostorg/uuid/pull/52#issuecomment-342609170  

All right;  
  
```  
template<class RI> void generate( RI first, RI last )  
{  
// static_assert-ing omitted for brevity  
    for( ; first != last; ++first )  
    {  
        this->generate_random_bytes( &*first, sizeof(*first) );  
        *first &= 0xFFFFFFFFu;  
    }  
}  
```  
  
Could also add  
  
```  
void generate( uint32_t* first, uint32_t* last )  
{  
    if( first != last )  
        this->generate_random_bytes( &*first, sizeof(*first) * (last - first) );  
}  
```

---

## Comment 17

> Username: jeking3  
> Created_at: 2017-11-07 20:31:50 UTC  
> Url: https://github.com/boostorg/uuid/pull/52#issuecomment-342612821  

I started adding these suggestions, however I am not supposed to use virtual inheritance where a base class would define get_random_bytes, so I cannot put something like this into a base class.  
  
In some implementations there is a single file with preprocessor macros to select different code based on platform.  With 5 platforms this is pretty unruly.  
  
Right now I have 5 ".ipp" files, and depending on the platform detected by macros, one of these files is included, and each one contains its own definition of random_provider.  I didn't want to have to cut and paste the same code 5 times though.  Still trying to figure out the right way to assemble all of this...  
  
I'm going to take a break for a little bit, as I suspect I'm missing something terribly obvious.

---

## Comment 18

> Username: pdimov  
> Created_at: 2017-11-07 20:41:32 UTC  
> Url: https://github.com/boostorg/uuid/pull/52#issuecomment-342615368  

Virtual inheritance?  
  
Just define `random_provider_base` in the `.ipp` files and inherit from it in `random_provider` without using virtual inheritance.  
  
```  
// random_provider_openbsd.ipp  
  
class random_provider_base  
{  
public:  
    // void get_random_bytes  
};  
```  
  
```  
// random_provider.hpp  
  
#if openbsd  
# include "random_provider_openbsd.ipp"  
#endif  
  
class random_provider: public random_provider_base  
{  
public:  
    // void generate  
};  
```  
  
What's your concern with this arrangement?

---

## Comment 19

> Username: jeking3  
> Created_at: 2017-11-07 21:45:57 UTC  
> Url: https://github.com/boostorg/uuid/pull/52#issuecomment-342632970  

@pdimov thanks, I knew I needed a break!

---

## Comment 20

> Username: pdimov  
> Created_at: 2017-11-07 22:55:27 UTC  
> Url: https://github.com/boostorg/uuid/pull/52#issuecomment-342651558  

Welcome.  
  
This is somewhat relevant: http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2016/p0205r0.html  
  
Our (Boost's) `SeedSeq` concept is fine though, it's only the standard one that's broken. :-)

---

## Comment 21

> Username: jeking3  
> Created_at: 2017-11-08 01:50:05 UTC  
> Url: https://github.com/boostorg/uuid/pull/52#issuecomment-342683783  

Don't see boost mentioned at all in the references?  Interesting.

---
