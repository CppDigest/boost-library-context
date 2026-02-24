# #143 - #include <boost/range/mfc.hpp> causes a compilation error [Open]

> Username: gabrielbocek  
> Created at: 2023-04-05 11:47:42 UTC  
> Updated at: 2024-01-12 10:17:35 UTC  
> Url: https://github.com/boostorg/range/issues/143  

Hi,  
I updated the visual C++ compiler from 17.0.6  to 17.4.6 and I'm getting a compilation error by including <boost/range/mfc.hpp>:  
  
1>\boost\range\detail\microsoft.hpp(626,33): error C2440: 'return': cannot convert from 'const void *' to 'const void *&'  
1>\boost\range\detail\microsoft.hpp(624,1): message : while compiling class template member function 'const void *&boost::range_detail_microsoft::list_iterator<const X,const void *,boost::use_default,boost::use_default>::dereference(void) const'  
1>        with  
1>        [  
1>            X=CPtrList  
1>        ]  
1>\boost\iterator\iterator_facade.hpp(631,11): message : see reference to function template instantiation 'const void *&boost::range_detail_microsoft::list_iterator<const X,const void *,boost::use_default,boost::use_default>::dereference(void) const' being compiled  
1>        with  
1>        [  
1>            X=CPtrList  
1>        ]  
1>\boost\range\mfc.hpp(881,1): message : see reference to class template instantiation 'boost::range_detail_microsoft::list_iterator<const X,const void *,boost::use_default,boost::use_default>' being compiled  
1>        with  
1>        [  
1>            X=CPtrList  
1>        ]  
  
The project is compiled with /std:c++20 switch.  
  
This is easily reproducible - it is just needed to create a C++ console application, add the include to <boost/range/mfc.hpp> to one cpp file, add boost directory to include directories and set the compiler flag /std:c++20.   
  
I'm using boost 1.81.  
  
Best regards,  
Gabriel

---

## Comment 1

> Username: malcolmdavey  
> Created at: 2023-04-11 07:28:40 UTC  
> Url: https://github.com/boostorg/range/issues/143#issuecomment-1502825479  

Same - VS 17.4.x, C++20

---

## Comment 2

> Username: tobias-loew  
> Created at: 2023-08-27 17:35:17 UTC  
> Url: https://github.com/boostorg/range/issues/143#issuecomment-1694722034  

I made a non-intrusive fix #144. But it isn't merged yet. 🙁

---

## Comment 3

> Username: gabrielbocek  
> Created at: 2023-08-28 07:18:27 UTC  
> Url: https://github.com/boostorg/range/issues/143#issuecomment-1695162106  

@tobias-loew  How can we speed up the merge? I see that you made the fix in April '23.

---

## Comment 4

> Username: tobias-loew  
> Created at: 2023-08-28 09:02:39 UTC  
> Url: https://github.com/boostorg/range/issues/143#issuecomment-1695314839  

It just happened! (Even without the need for defining the macro: the old code was broken anyway.)

---

## Comment 5

> Username: gabrielbocek  
> Created at: 2023-12-27 17:27:38 UTC  
> Url: https://github.com/boostorg/range/issues/143#issuecomment-1870495247  

Somehow this fix is still not part of the Boost 1.84. Can you please clarify?

---

## Comment 6

> Username: tobias-loew  
> Created at: 2024-01-03 06:31:30 UTC  
> Url: https://github.com/boostorg/range/issues/143#issuecomment-1874905672  

@neilgroves Any chance to get #144 fix into the boost-release?   
(There are several other commits also waiting for release. The last merge into master was May 2021)

---

## Comment 7

> Username: neilgroves  
> Created at: 2024-01-12 10:17:34 UTC  
> Url: https://github.com/boostorg/range/issues/143#issuecomment-1888817251  

Yes I can merge to master when I return to the UK in a few days.  
  
Thanks,  
Neil  
  
On Wed, 3 Jan 2024, 06:31 Tobias Loew, ***@***.***> wrote:  
  
> @neilgroves <https://github.com/neilgroves> Any chance to get #144  
> <https://github.com/boostorg/range/pull/144> fix into the boost-release?  
> (There are several other commits also waiting for release. The last merge  
> into master was May 2021)  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/range/issues/143#issuecomment-1874905672>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABTIJ2SROATK42WVKKWUWC3YMT3MZAVCNFSM6AAAAAAWT62X5CVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMYTQNZUHEYDKNRXGI>  
> .  
> You are receiving this because you were mentioned.Message ID:  
> ***@***.***>  
>
