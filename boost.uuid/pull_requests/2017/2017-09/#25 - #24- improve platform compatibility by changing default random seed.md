# #25 #24: improve platform compatibility by changing default random seed [Closed]

> Username: jeking3  
> Created at: 2017-09-04 20:22:06 UTC  
> Updated at: 2017-10-18 19:48:00 UTC  
> Closed at: 2017-09-07 17:46:30 UTC  
> Url: https://github.com/boostorg/uuid/pull/25  

from uuid internal code which was not friendly to all platforms, to boost::random::random_device which is  
I'm submitting this PR for comments.  
  
@eldiener this requires a new dependency for some use cases; folks may require linking against boost::random if they are using boost::uuid and relying on the default random generator constructor whereas before the code was platform specific and not always compatible.  
  
Summary:  
  
1. Moved seed_rng into detail/  
2. Removed all platform-specific logic in seed_rng and changed it to leverage boost::random::random_device.  
3. Updated documentation and comments appropriate to change.  
  
Should consider moving seed_rng into boost::random at some point to make it easier to use seed_seq, or fastly simplify boost::random seeding of PRNGs by allowing each PRNG to take a boost::random::random_device as an argument to seed().

---

## Comment 1

> Username: eldiener  
> Created_at: 2017-09-04 23:46:27 UTC  
> Url: https://github.com/boostorg/uuid/pull/25#issuecomment-327039953  

Why should just changing the order of the header files in random_generator.hpp ever matter ?

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-09-05 03:48:38 UTC  
> Url: https://github.com/boostorg/uuid/pull/25#issuecomment-327064383  

While header order should not (and in this case does not) matter, I prefer  
to have headers ordered alphabetically with the exception of the header for  
the module it is implementing which belongs first.  
  
On Mon, Sep 4, 2017 at 7:46 PM, Edward Diener <notifications@github.com>  
wrote:  
  
> Why should just changing the order of the header files in  
> random_generator.hpp ever matter ?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/uuid/pull/25#issuecomment-327039953>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ALOdbZDoekex09SyJmzfcpTGGcW2Hor2ks5sfIvVgaJpZM4PMOlu>  
> .  
>

---

## Review 3 [Commented]

> Username: jeking3  
> Created_at: 2017-09-05 03:51:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/25#pullrequestreview-60490746  

📁 include/boost/uuid/detail/seed_rng.hpp

```diff
  30 |+ inline void seed(UniformRandomNumberGenerator& rng)
  31 |+ {
  32 |+     BOOST_CONSTEXPR_OR_CONST size_t entropy_len = 16; // 512 bytes of entropy
```

> Username: jeking3  
> Created_at: 2017-09-05 03:51:17 UTC  
> Updated_at: 2017-09-05 16:36:12 UTC  
> Url: https://github.com/boostorg/uuid/pull/25#discussion_r136890848  

Should boost/config.hpp be added as an include to satisfy BOOST_CONSTEXPR_OR_CONST usage here?


---

## Comment 4

> Username: strentler  
> Created_at: 2017-09-06 09:32:10 UTC  
> Url: https://github.com/boostorg/uuid/pull/25#issuecomment-327429563  

@jeking3 : i have applied your patch. It does the job for UWP now.  Have tested this with SDK version 10.0.15063.0. I have also checked if it goes through the "Windows App Cert Kit".  It does.  
Thank you!

---

## Comment 5

> Username: jeking3  
> Created_at: 2017-09-06 13:51:05 UTC  
> Url: https://github.com/boostorg/uuid/pull/25#issuecomment-327489430  

@eldiener or anyone else on the CTP team able to do a code review?

---

## Review 6 [Commented]

> Username: Lastique  
> Created_at: 2017-09-07 08:47:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/25#pullrequestreview-61151831  

📁 include/boost/uuid/detail/seed_rng.hpp

```diff
  33 |+     BOOST_STATIC_CONSTEXPR size_t entropy_len = 32; // 256 bits of entropy
  34 |+     boost::random::random_device rd;
  35 |+     std::vector<unsigned int> entropy(entropy_len);
```

> Username: Lastique  
> Created_at: 2017-09-07 08:47:25 UTC  
> Url: https://github.com/boostorg/uuid/pull/25#discussion_r137480705  

This should be an array if the size is known in compile time (it is in your case).


---

## Review 7 [Commented]

> Username: Lastique  
> Created_at: 2017-09-07 08:47:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/25#pullrequestreview-61151964  

📁 include/boost/uuid/detail/seed_rng.hpp

```diff
  31 |+ inline void seed(UniformRandomNumberGenerator& rng)
  32 |+ {
  33 |+     BOOST_STATIC_CONSTEXPR size_t entropy_len = 32; // 256 bits of entropy
```

> Username: Lastique  
> Created_at: 2017-09-07 08:47:58 UTC  
> Url: https://github.com/boostorg/uuid/pull/25#discussion_r137480806  

`std::size_t`. Also, `#include <cstddef>` is missing.


---

## Review 8 [Commented]

> Username: Lastique  
> Created_at: 2017-09-07 08:48:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/25#pullrequestreview-61152122  

📁 include/boost/uuid/detail/seed_rng.hpp

```diff
  34 |+     boost::random::random_device rd;
  35 |+     std::vector<unsigned int> entropy(entropy_len);
  36 |+     std::generate(entropy.begin(), entropy.end(), std::ref(rd));
```

> Username: Lastique  
> Created_at: 2017-09-07 08:48:36 UTC  
> Url: https://github.com/boostorg/uuid/pull/25#discussion_r137480981  

`std::ref` is not available in C++03, which the rest of the library is compatible with.


---

## Review 9 [Commented]

> Username: Lastique  
> Created_at: 2017-09-07 08:50:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/25#pullrequestreview-61152686  

📁 include/boost/uuid/detail/seed_rng.hpp

```diff
  41 |+ // random_device itself does not / can not be seeded
  42 |+ template <>
  43 |+ inline void seed<boost::random::random_device>(boost::random::random_device&) {}
```

> Username: Lastique  
> Created_at: 2017-09-07 08:50:43 UTC  
> Url: https://github.com/boostorg/uuid/pull/25#discussion_r137481487  

This should probably be an overload instead of specialization.


---

## Comment 10

> Username: Lastique  
> Created_at: 2017-09-07 08:56:19 UTC  
> Url: https://github.com/boostorg/uuid/pull/25#issuecomment-327736557  

I would rather keep `seed_rng` so that it is possible to generate UUIDs without linking with Boost.Random. `seed_rng` uses system sources for getting random data that are ultimately used by Boost.Random, so there's little reason to bring in that dependency. I would rather have Boost.Random-based version separate, if possible, and without breaking backward compatibility.

---

## Comment 11

> Username: jeking3  
> Created_at: 2017-09-07 17:00:22 UTC  
> Url: https://github.com/boostorg/uuid/pull/25#issuecomment-327861258  

@Lastique my rationale for eliminating the platform-specific code in seed_rng was to avoid situations such as that described in #24.  Given there is already a documented, supported, and highly portable seed generator in boost::random::random_device, it makes less sense to me to have a similar method in uuid with platform specific code that needs to be maintained.  Could you explain further how you believe the change I made is not backwards compatible?  seed_rng is in the detail namespace and it is my understanding that these do not need to maintain backwards compatibility.    
  
As with all projects, I am looking to minimize the maintenance cost of the library by leveraging that which already exists and likely does a far better job than the code that I removed.  It should be intuitive from a user perspective that if they are using random_generator to make randomized UUIDs, that they would need to link to boost::random.  
  
Thank you for your other code review comments, I will make changes to address them.

---

## Comment 12

> Username: Lastique  
> Created_at: 2017-09-07 17:19:46 UTC  
> Url: https://github.com/boostorg/uuid/pull/25#issuecomment-327866528  

On 09/07/17 20:00, James E. King, III wrote:  
> my rationale for eliminating the   
> platform-specific code in seed_rng was to avoid situations such as that   
> described in #24 <https://github.com/boostorg/uuid/issues/24>. Given   
> there is already a documented, supported, and highly portable seed   
> generator in boost::random::random_device, it makes less sense to me to   
> have a similar method in uuid with platform specific code that needs to   
> be maintained.  
  
I don't think the cost of maintenance here is significant enough when   
compared to having Boost.UUID a header-only library. In some projects,   
header-only property is a deal breaker.  
  
The #24 needs to be investigated. I'm not familiar with the UWP   
specifics, but I'm sure there must be an API for accessing system RNG.  
  
> Could you explain further how you believe the change I   
> made is not backwards compatible? seed_rng is in the detail namespace   
> and it is my understanding that these do not need to maintain backwards   
> compatibility.  
  
Having to link with Boost.Random is a breaking change.  
  
> It should be intuitive from   
> a user perspective that if they are using random_generator to make   
> randomized UUIDs, that they would need to link to boost::random.  
  
No, I'm sorry, that doesn't follow. Boost.Random is a separate library   
and Boost.UUID is not obliged to use it if there is enough reason not   
to. IMHO, header-only property is important enough to avoid the linking   
dependency on Boost.Random, especially since the seeding code is already   
written and works on most platforms (except UWP, that is). Adding   
support for another platform to the existing implementation seems more   
preferable to me.

---

## Comment 13

> Username: jeking3  
> Created_at: 2017-09-07 17:46:30 UTC  
> Url: https://github.com/boostorg/uuid/pull/25#issuecomment-327873356  

@Lastique your argument for header-only is convincing enough for me to reconsider.  I do wonder though if seed_rng belongs in boost::random more than in uuid, since there will be subject-matter experts there who will be able to deal with the issue of platform compatibility should it pop up again in the future.  For the time being I will close out this change and focus on fixing the specific bug reported.

---

## Comment 14

> Username: Lastique  
> Created_at: 2017-09-07 17:54:41 UTC  
> Url: https://github.com/boostorg/uuid/pull/25#issuecomment-327875602  

> I do wonder though if seed_rng belongs in boost::random more than in uuid  
  
This is probably true. `seed_rng` wouldn't be needed if `boost::random_device` was header-only.

---
