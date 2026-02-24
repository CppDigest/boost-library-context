# #30 add missing quantities [Open]

> Username: e-kwsm  
> Created at: 2018-07-23 09:04:56 UTC  
> Updated at: 2021-06-10 02:34:19 UTC  
> Url: https://github.com/boostorg/units/pull/30  

- energy_density  
- heat_capacity  
- luminance  
- molar_energy  
- molar_heat_capacity  
- specific_energy  
- specific_heat_capacity  
- specific_volume  
- stress  
- thermal_conductivity  
  
Enable something like  
```cpp  
boost::units::quantity<boost::units::si::specific_heat_capacity> c =  
    4.2e3 * boost::units::si::joule / boost::units::si::kelvin / boost::units::si::kilogram;  
```

---

## Comment 1

> Username: correaa  
> Created_at: 2018-07-24 03:57:17 UTC  
> Url: https://github.com/boostorg/units/pull/30#issuecomment-407273289  

Hello, I would like to contribute code that I developed during many years to make Boost.Units usable for my applications. Including an atomic units system. The code can be found here https://gitlab.com/correaa/boost-units, it is made to coexists with a current installation of boost.

---

## Comment 2

> Username: e-kwsm  
> Created_at: 2018-07-27 18:36:46 UTC  
> Url: https://github.com/boostorg/units/pull/30#issuecomment-408504248  

@correaa It seems that I have no permission to view your repo.  
Anyway you can create new PR.

---

## Comment 3

> Username: correaa  
> Created_at: 2018-07-28 00:22:22 UTC  
> Url: https://github.com/boostorg/units/pull/30#issuecomment-408568403  

Thanks EK, sorry about that. I fixed that now, would you like to take a  
look again? Just to have some feedback if this is something that the  
Boost.Units maintainer would in principle be interested in.  
  
On Fri, Jul 27, 2018 at 11:36 AM E Kawashima <notifications@github.com>  
wrote:  
  
> @correaa <https://github.com/correaa> It seems that I have no permission  
> to view your repo.  
> Anyway you can create new PR.  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/units/pull/30#issuecomment-408504248>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AC6BU0dqWo5sIf5dym2ujcYX3tMd6ZCHks5uK12_gaJpZM4Van1q>  
> .  
>

---

## Comment 4

> Username: e-kwsm  
> Created_at: 2018-07-28 18:40:53 UTC  
> Url: https://github.com/boostorg/units/pull/30#issuecomment-408627251  

I am not a developer of this library, just a user. But I appreciate that you implemented atomic unit system.  
  
Here are brief comments:  
  
* `systems/au/si_conversion.hpp`: some of converting factors are different from (older than?) 2014 CODATA recommended values; how about using `include/boost/units/systems/si/codata/`? (#29)  
* underscore + ALPHABET (e.g. `_TEST_BOOST_UNITS_AU`) should be avoided (N4762 5.10)  
    > Each identifier that contains a double underscore `__` or begins with an underscore followed by an  
uppercase letter is reserved to the implementation for any use.  
* Spaces should be used for indent (https://www.boost.org/development/requirements.html#Tabs)

---

## Comment 5

> Username: alfC  
> Created_at: 2018-07-28 21:45:06 UTC  
> Url: https://github.com/boostorg/units/pull/30#issuecomment-408636668  

On Sat, Jul 28, 2018 at 11:40 AM E Kawashima <notifications@github.com>  
wrote:  
  
> I am not a developer of this library, just a user. But I appreciate that  
> you implemented atomic unit system.  
>  
Thank you for the feedback EK. I will try to contact the current maintainer.  
Ideally I would have send all this as a github issue but I couldn't find  
how to submit issues here.​  
  
> Here are brief comments:  
>  
>    - systems/au/si_conversion.hpp: some of converting factors are  
>    different from (older than?) 2014 CODATA recommended values; how about  
>    using include/boost/units/systems/si/codata/? (#29  
>    <https://github.com/boostorg/units/pull/29>)  
>  
> ​Yes, that is in the plan (I have comments referring to the codata value  
that should be used instead).  
  
for example I have to pull  
`boost::units::si::constants::codata::m_e.value().value()`. The  
`value().value()` is funny though.  
  
  
I was hoping I find a way to do it automatically without coding one by one.  
  
Anyway, I now implemented all the conversions between au and si in terms of  
codata information from the other parts of the library.  
  
Still not sure if `systems/au/si_conversion.hpp` is the best place to put  
these conversion.  
  
>  
>    - underscore + ALPHABET (e.g. _TEST_BOOST_UNITS_AU) should be avoided  
>    (N4762 5.10)  
>  
>    Each identifier that contains a double underscore __ or begins with an  
>    underscore followed by an  
>    uppercase letter is reserved to the implementation for any use.  
>  
>  
> ​This code will disappear when I put the test in a separate directory.​  
It is now removed from `systems/au/si_conversion.hpp` for example.  
  
>  
>    -  
>  
>    - Spaces should be used for indent (  
>    https://www.boost.org/development/requirements.html#Tabs)  
>  
> ​Yes, I guess I will do a search an replace for 4 (?) spaces per tab  
before submitting each file.  
  
Thank you for the feedback.  
Alfredo​  
  
  
  
>  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/units/pull/30#issuecomment-408627251>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ACMfYKRx4Wa65SF4NdOwd4n0vLH2PoW2ks5uLLA1gaJpZM4Van1q>  
> .  
>

---

## Comment 6

> Username: jhunold  
> Created_at: 2018-09-14 08:35:21 UTC  
> Url: https://github.com/boostorg/units/pull/30#issuecomment-421273856  

I'd like you to change the copyright header. Even if they are copied and changed, new files should get new copyright.

---

## Comment 7

> Username: e-kwsm  
> Created_at: 2018-09-18 02:08:29 UTC  
> Url: https://github.com/boostorg/units/pull/30#issuecomment-422229510  

> I'd like you to change the copyright header. Even if they are copied and changed, new files should get new copyright.  
  
What should I modify? Just add my name?

---

## Comment 8

> Username: correaa  
> Created_at: 2018-09-18 02:15:21 UTC  
> Url: https://github.com/boostorg/units/pull/30#issuecomment-422230645  

Sorry, is the request for a copyright header for me (Alfredo Correa) or for  
E. Kawashima? (these are potentially two separate contributions).  
  
On Mon, Sep 17, 2018 at 7:08 PM E Kawashima <notifications@github.com>  
wrote:  
  
> I'd like you to change the copyright header. Even if they are copied and  
> changed, new files should get new copyright.  
>  
> What should I modify? Just add my name?  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/units/pull/30#issuecomment-422229510>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/AC6BU9iftJQPwVE6fYK_a3-pBGvpOnjyks5ucFWdgaJpZM4Van1q>  
> .  
>

---

## Comment 9

> Username: jhunold  
> Created_at: 2018-09-19 17:40:19 UTC  
> Url: https://github.com/boostorg/units/pull/30#issuecomment-422893984  

I thought about this MR, but this applies to upcoming ones, too   
Rule of thumb:  
  
- when creating new files copyright them under your name  
- major changes to existing files add your name to the existing copyright holder(s)  
- trivial changes no copyright changes  
  
For changing the CODATA constants the latter applies. The constants are public domain after all.  
For augmenting the builtin quantities the first applies in my eyes. If you think the original copyright should be kept, this would be fine with me. This is what the BSL is all about, after all.  
  
This allows a quick check of who added which constant when without heavy usage of `git log`

---

## Comment 10

> Username: e-kwsm  
> Created_at: 2018-09-21 01:34:16 UTC  
> Url: https://github.com/boostorg/units/pull/30#issuecomment-423385749  

I updated the copyright.

---
