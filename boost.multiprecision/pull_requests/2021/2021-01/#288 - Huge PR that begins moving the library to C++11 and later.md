# #288 Huge PR that begins moving the library to C++11 and later. [Merged]

> Username: jzmaddock  
> Created at: 2021-01-14 19:28:07 UTC  
> Updated at: 2021-02-04 11:26:26 UTC  
> Merged at: 2021-02-04 11:26:26 UTC  
> Closed at: 2021-02-04 11:26:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/288  



---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-01-16 08:21:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/288#issuecomment-761526749  

This is amazing, John. Thank you for leading this incredible work.  
  
It is great to see things like `<cstdint>`, `std::enable_if`, `static_assert` and `constexpr`. Regarding the word `constexpr` I believe, as the AppVeyor selection of tests indicates, that this lowly workd finally arrived on the MSVC scene for `_MSC_VER >= 1400`. Other features came sooned on this particular compiler.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-01-16 11:31:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/288#issuecomment-761548361  

Thanks Chris, just removed all MPL usage as well.... a bit of brain dead editing was just the ticket for the current lockdown! ;)  There might be a couple more changes to come, but this is mostly there now I think.  
  
With regard to msvc: MSVC-14.0up3 first fully supported `constexpr` it seems, `noexcept` was also missing before that, and also vc12 had some issues with not finding the correct function overloads in some cases (enable_if not working properly), so yes, vc14 will be the first supported MSVC compiler after this change.

---

## Comment 3

> Username: madhur4127  
> Created_at: 2021-01-16 11:50:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/288#issuecomment-761550782  

Wow John, that's so much work. Thanks!  
  
What's the C++ base standard that you plan to support? Like full comparability for C++11 for upcoming features or you want to move to new features like Boost.Math?

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-01-16 13:00:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/288#issuecomment-761559656  

>What's the C++ base standard that you plan to support?  
  
C++11.  
  
We've signalled the change for a year now, so it's time to move on.  There are actually some things which would be both more efficient to compile and easier to implement with C++17's `if constexpr`, but that will have to wait for a few years yet!  
  
In the mean time, something seems to have broken CI...

---

## Comment 5

> Username: pabristow  
> Created_at: 2021-01-16 17:36:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/288#issuecomment-761603068  

I'm not surprised the CI has indigestion!  Congratulations on a tremendous step forward and a major achievement in one giant leap.  
  
I know we have given notice for over a year that it was going to happen, but now that it really will with the next release, I wonder if we need to give a much louder notice that the current release is their last release for people who are stuck with pre-c++11 compilers?  
  
(and perhaps to give notice now that a move to minimum C++17 will take place two years hence to really focus peoples mind on the need to plan to 'KEEP UP'.)

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2021-01-16 19:01:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/288#issuecomment-761616522  

>I know we have given notice for over a year that it was going to happen, but now that it really will with the next release, I wonder if we need to give a much louder notice that the current release is their last release for people who are stuck with pre-c++11 compilers?  
  
Other than a very loud announcement in the release notes, I'm not sure what we can do?  I will add some #errors to the headers to catch C++03 compiles as well.  
  
>(and perhaps to give notice now that a move to minimum C++17 will take place two years hence to really focus peoples mind on the need to plan to 'KEEP UP'.)  
  
Might be a bit soon?  I don't think any of the main compilers are C++17 by default yet.... at the current rate it will be nearer 2030 before we can move again !!

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2021-01-16 22:14:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/288#issuecomment-761688161  

>> I wonder if we need to give a much louder notice that the current release is their last release for people who are stuck with pre-c++11 compilers?  
  
> Other than a very loud announcement in the release notes, I'm not sure what we can do? I will add some #errors to the headers to catch C++03 compiles as well.  
  
I guess on a practical side, it might really be more clear to add some purposeful `#error` compile-time messages or else get potentially more confusing error messages when a non-C++11 compiler runs into either library headers from C++11 (like `<cstdint>`) or core language changes in C++11 such as `constexpr`.

---

## Comment 8

> Username: NAThompson  
> Created_at: 2021-01-16 22:51:55 UTC  
> Updated_at: 2021-01-16 22:52:24 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/288#issuecomment-761692775  

> Might be a bit soon? I don't think any of the main compilers are C++17 by default yet.... at the current rate it will be nearer 2030 before we can move again !!  
  
They aren't default, but they are compatible. Default on our HPC systems is now gcc 9.3.

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2021-01-23 16:15:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/288#issuecomment-766111894  

Here are some points we could still refine (and I'm happy to contribute to the editing of some, all or none of these as we may decide...)  
  
1) Some constant expressions using `sizeof()` could be `constexpr`. An example is here:   
![grafik](https://user-images.githubusercontent.com/2404721/105607177-a9813d00-5d9d-11eb-9d99-df5055bb0bb9.png)  
  
2) Some places could use either `constexpr` or sometimes in classes `static constexpr` for PODs depending on the context, such as here:   
![grafik](https://user-images.githubusercontent.com/2404721/105607246-fd8c2180-5d9d-11eb-9c42-76b505cce5b8.png)  
  
3) A few places could make more refined use of `<type_traits>`. Not many, I found one here (`std::is_same<>::value` OK with `constexpr if`:   
![grafik](https://user-images.githubusercontent.com/2404721/105607304-480d9e00-5d9e-11eb-886d-84c1797570f9.png)  
  
4) We could change nearly 1,000 instances of `typedef` with `using`. This is in the spirit of modern C++11.  
  
After that, i would like to take a more close look at move semantics and ensure that we are getting advantages full of these. I'm not so good at that language area, so need help here.  
  
As mentioned, i would be happy to edit lots of this if desired. i am learning a lot on this issue.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2021-01-23 19:11:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/288#issuecomment-766163663  

>Some constant expressions using sizeof() could be constexpr. An example is here:  
  
Nod. Lots of those, testing locally now...  
  
>Some places could use either constexpr or sometimes in classes static constexpr for PODs depending on the context,   
  
Nod.   As above.  
  
> A few places could make more refined use of <type_traits>. Not many, I found one here (std::is_same<>::value OK with constexpr if  
  
I don't see that one: it's using `if constexpr` already in C++17 mode, and using `is_same` here would not be the same thing?  
  
> We could change nearly 1,000 instances of typedef with using. This is in the spirit of modern C++11.  
  
I tend to be a bit of a Luddite on things like this - it's a shiny new feature just the same as the old feature ;)  But it's probably just a search and replace away so I have no real strong feelings on the matter.  
  
> After that, i would like to take a more close look at move semantics and ensure that we are getting advantages full of these. I'm not so good at that language area, so need help here.  
  
Move semantics were reasonably well baked in from the start I hope, but I'm sure there's always more that could be done!  
  
Many thanks for these.

---

## Comment 11

> Username: ckormanyos  
> Created_at: 2021-01-23 20:21:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/288#issuecomment-766173567  

>> A few places could make more refined use of <type_traits>. Not many, I found one here (std::is_same<>::value OK with `constexpr if`  
  
> I don't see that one  
  
You are right. My finding was wrong because `double` and `long double` are not `is_same`. My mistake.

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2021-01-25 09:42:15 UTC  
> Updated_at: 2021-01-25 09:43:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/288#issuecomment-766688132  

>> We could change nearly 1,000 instances of typedef with using. This is in the spirit of modern C++11.  
  
> on things like this - it's a shiny new feature just the same as the old feature ;) But it's probably just a search and replace away so I have no real strong feelings on the matter.  
  
Unfortunately, this would be a more didicated edit effort because the order of `typedef` and `using` (an alias) is exactly reversed regarding which type is known and which is being defined. So we would have to find each of 947 instances of `typedef`, switch to `using` and reverse the order of the types in the statements.  
  
This is, in fact a shiny new thing that I would like to see and be proud to have. What if I handled this? Or ultimately decided to let it slide? Would you like the code with `using` or is it more like you resist and do not even want that new feature? I can live with either, but I am willing to work for using `using`...  
  
I wonder if this is too technically challenging (or non-challenging?) for a GSoC 2021? The length of the GSoc has been cut in half this year to 6 weeks. This editing and testing effort could be the right thing... Or I could just do it myself, or let it go?  
  
@jzmaddock: Thoughts on my ramblings...?

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2021-01-25 13:45:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/288#issuecomment-766826365  

> Unfortunately, this would be a more didicated edit effort because the order of typedef and using (an alias) is exactly reversed regarding which type is known and which is being defined. So we would have to find each of 947 instances of typedef, switch to using and reverse the order of the types in the statements.  
  
s/typedef\s+(.+)\s+([a-zA-Z0-9]+);/using $2 = $1;/g  
  
Does the lot in about 15 seconds :)

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2021-01-25 15:23:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/288#issuecomment-766892329  

>> a more didicated edit effort because the order of typedef and using (an alias) is exactly reversed  
  
> s/typedef\s+(.+)\s+([a-zA-Z0-9]+);/using $2 = $1;/g  
  
Awesome! Thank you for this.

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2021-01-31 18:22:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/288#issuecomment-770426508  

OK I have one failure left that's a CI configuration (out of memory) issue that I'll fix shortly.  
  
Other than that, I think this is ready to merge.  Anyone have any comments or last objections?

---

## Comment 16

> Username: ckormanyos  
> Created_at: 2021-02-01 06:48:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/288#issuecomment-770615636  

> this is ready to merge. Anyone have any comments or last objections?  
  
It is great. Many thanks for the dedicated, detailed, eloquent migration. It's great to see so much beautiful C++11 in Multiprecision!  
  
There is remaining work in CI for GHA (adding compilers and language specifications). These can be handled in #291

---

## Comment 17

> Username: ckormanyos  
> Created_at: 2021-02-04 10:55:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/288#issuecomment-773218971  

> There is remaining work in CI for GHA (adding compilers and language specifications).  
  
Old GCC and clang compilers 5/6 have been added in #291. This brings the test matrices to the same level as those in Math. One test for C++1z on clang was disabled on source code level. After that the GHA are green.  
  
The GCC/clang v5/6 tests on xenial in #291 can be merged into #288. Alternatively, we could mesynchronize these two branches and merge to develop, thereby I might suggest moving toward closing bot #288 and #291.  
  
Thoughts?

---
