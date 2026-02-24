# #32 changed angled brackets with quotes for gcc include file in preproces… [Merged]

> Username: mihaipop11  
> Created at: 2018-02-15 09:27:36 UTC  
> Updated at: 2018-02-26 19:37:07 UTC  
> Merged at: 2018-02-26 17:31:21 UTC  
> Closed at: 2018-02-26 17:31:22 UTC  
> Url: https://github.com/boostorg/mpl/pull/32  

Fix to ticket #13449

---

## Comment 1

> Username: mclow  
> Created_at: 2018-02-15 14:10:23 UTC  
> Url: https://github.com/boostorg/mpl/pull/32#issuecomment-365937810  

I have no opinion on this specific change, but in general, headers in the `boost/` hierarchy are included via angle-brackets.   However, I've read the trac issue, so I see where you're coming from.

---

## Comment 2

> Username: mihaipop11  
> Created_at: 2018-02-15 16:55:34 UTC  
> Url: https://github.com/boostorg/mpl/pull/32#issuecomment-365991002  

i can understand that a general guideline is nice to have for the sources, another fix for this would be to disable the GNU Extensions by compiling with `-std=c++{98,11,14,17,2a}` but this is not a generic solution

---

## Comment 3

> Username: mihaipop11  
> Created_at: 2018-02-15 17:00:19 UTC  
> Updated_at: 2018-02-15 17:09:51 UTC  
> Url: https://github.com/boostorg/mpl/pull/32#issuecomment-365992636  

i can see how you think that probably is not worth it just because someone tries to run the script in a folder named `linux`, normally i wouldn't care about this minor thing, but lets take this example: in a `yocto project` context it is a high change to have in the path a folder named `linux` and it's not that nice to fix it

---

## Comment 4

> Username: eldiener  
> Created_at: 2018-02-26 02:49:22 UTC  
> Url: https://github.com/boostorg/mpl/pull/32#issuecomment-368375311  

Are you saying that the mnemonic 'linux' is a macro under gcc ?

---

## Comment 5

> Username: mclow  
> Created_at: 2018-02-26 14:09:14 UTC  
> Url: https://github.com/boostorg/mpl/pull/32#issuecomment-368514793  

> Are you saying that the mnemonic 'linux' is a macro under gcc ?  
  
Yes, that's what he's saying.  
Along with a whole bunch of other names. :-(  
Use `gcc -dM -E - < /dev/null` to show them all.  
Fortunately most (but not all) of them start with a double underscore.

---

## Comment 6

> Username: mihaipop11  
> Created_at: 2018-02-26 14:21:54 UTC  
> Updated_at: 2018-02-26 15:16:53 UTC  
> Url: https://github.com/boostorg/mpl/pull/32#issuecomment-368518353  

indeed, before ANSI i believe, there was no standard regarding these macros so the developers just used ``linux``, ``unix``... , then when the ANSI standard was created, the developers reached the conclusion that the macros should start with double underscore and end with double underscore ex: ``__linux__``

---

## Comment 7

> Username: eldiener  
> Created_at: 2018-02-26 16:29:32 UTC  
> Url: https://github.com/boostorg/mpl/pull/32#issuecomment-368561178  

As long as this does not affect anything negatively I can merge this. Is there any case in which the switch from <> to "" might cause any problems in the gcc invocation with current versions of gcc ?

---

## Comment 8

> Username: mihaipop11  
> Created_at: 2018-02-26 16:41:43 UTC  
> Url: https://github.com/boostorg/mpl/pull/32#issuecomment-368565332  

not that i know of. i tried with gcc5-6-7, in all cases there were no problems after the patch was applied.

---
