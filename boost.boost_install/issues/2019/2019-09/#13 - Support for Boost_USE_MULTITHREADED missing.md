# #13 - Support for Boost_USE_MULTITHREADED missing [Closed]

> Username: dennisklein  
> Created at: 2019-09-19 16:18:41 UTC  
> Updated at: 2019-09-20 19:20:34 UTC  
> Closed at: 2019-09-20 19:20:34 UTC  
> Url: https://github.com/boostorg/boost_install/issues/13  

See https://gitlab.kitware.com/cmake/cmake/issues/19714.  
  
I have not verified if one can legally end up in this state or if the user has installed non-mt and mt variants into the same existing prefix.  
* If it is legal, we would need support for [`Boost_USE_MULTITHREADED`](https://gitlab.kitware.com/cmake/cmake/blob/v3.15.3/Modules/FindBoost.cmake#L134-135),  
* if it is illegal, we should probably replace the variant globbing with an explicit list or clean the cmake package directories before installing.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-09-19 16:24:24 UTC  
> Url: https://github.com/boostorg/boost_install/issues/13#issuecomment-533207461  

I never expected anyone to still use single-threaded builds, let alone install both. Looks like I was wrong in this assumption of mine.

---

## Comment 2

> Username: drjasonharrison-vp-eio  
> Created at: 2019-09-19 17:04:27 UTC  
> Url: https://github.com/boostorg/boost_install/issues/13#issuecomment-533222277  

Note that the home brew command `brew install boost` was used to install the installation of boost with non-mt and mt variants into the same existing prefix.  
  
So the brew bottle contained both variants.

---

## Comment 3

> Username: dennisklein  
> Created at: 2019-09-19 17:10:13 UTC  
> Url: https://github.com/boostorg/boost_install/issues/13#issuecomment-533224226  

> So the brew bottle contained both variants.   
  
Thx, that answers my question above (homebrew uses [`threading=multi,single`](https://github.com/Homebrew/homebrew-core/blob/d20165d0f4527fe24cdbfd9cd06e4fb4d088b32b/Formula/boost.rb#L53)).

---

## Comment 4

> Username: pdimov  
> Created at: 2019-09-20 14:57:34 UTC  
> Url: https://github.com/boostorg/boost_install/issues/13#issuecomment-533588507  

Should be fixed now.

---

## Comment 5

> Username: dennisklein  
> Created at: 2019-09-20 18:40:28 UTC  
> Url: https://github.com/boostorg/boost_install/issues/13#issuecomment-533666706  

> Should be fixed now.  
  
Hm, but the default is still depending on what the globbing returns last, is it not? If `Boost_USE_MULTITHREADED` is not defined, [it shall default to choosing the mt variant](https://gitlab.kitware.com/cmake/cmake/blob/v3.15.3/Modules/FindBoost.cmake#L134-135), if available.

---

## Comment 6

> Username: pdimov  
> Created at: 2019-09-20 18:54:47 UTC  
> Url: https://github.com/boostorg/boost_install/issues/13#issuecomment-533671192  

I know that this is how FindBoost works, but BoostConfig (in the way things are currently architected) can't do "use X if available, else Y." Whether a variant is accepted or rejected cannot depend on whether another variant is available.

---

## Comment 7

> Username: dennisklein  
> Created at: 2019-09-20 19:20:34 UTC  
> Url: https://github.com/boostorg/boost_install/issues/13#issuecomment-533679062  

> I know that this is how FindBoost works, but BoostConfig (in the way things are currently architected) can't do "use X if available, else Y." Whether a variant is accepted or rejected cannot depend on whether another variant is available.  
  
In this matter, I am anyways just the messenger. But if you think it is a good idea to change the existing `find_package(Boost)` UX here, it is your right to do so, even though I personally think the argument you presented for it is rather poor. Thx, I guess the issue is resolved then.
