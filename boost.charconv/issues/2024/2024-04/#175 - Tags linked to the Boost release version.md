# #175 - Tags linked to the Boost release version [Closed]

> Username: yurybura  
> Created at: 2024-04-02 11:02:32 UTC  
> Updated at: 2024-04-15 19:25:27 UTC  
> Closed at: 2024-04-15 19:24:24 UTC  
> Url: https://github.com/boostorg/charconv/issues/175  

All libraries have git tags linked to the Boost release version, except charconv. Could you add tag boost-1.85.0.beta1?

---

## Comment 1

> Username: pdimov  
> Created at: 2024-04-02 11:20:56 UTC  
> Url: https://github.com/boostorg/charconv/issues/175#issuecomment-2031746385  

These tags are created by the release manager (@mclow ). It's not clear why Charconv doesn't have them.

---

## Comment 2

> Username: pdimov  
> Created at: 2024-04-02 11:23:22 UTC  
> Url: https://github.com/boostorg/charconv/issues/175#issuecomment-2031751668  

Boost.Scope doesn't seem to have a tag either.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-04-02 12:08:10 UTC  
> Url: https://github.com/boostorg/charconv/issues/175#issuecomment-2031869379  

The `--init` option is probably missing from `git submodule update` somewhere in the release scripts/procedures. :-)

---

## Comment 4

> Username: mclow  
> Created at: 2024-04-15 18:41:32 UTC  
> Url: https://github.com/boostorg/charconv/issues/175#issuecomment-2057573863  

Fixed.

---

## Comment 5

> Username: mborland  
> Created at: 2024-04-15 19:06:42 UTC  
> Updated at: 2024-04-15 19:12:46 UTC  
> Url: https://github.com/boostorg/charconv/issues/175#issuecomment-2057614798  

The boost-1.85.0 tag is on current develop and the boost-1.85.0.beta1 tag is against a random pre-review merge commit. What actually went out in release since the tags are seemingly incorrect (i.e. not aligned to master)?

---

## Comment 6

> Username: mclow  
> Created at: 2024-04-15 19:13:41 UTC  
> Url: https://github.com/boostorg/charconv/issues/175#issuecomment-2057625680  

Dang - can't get anything right today :-(  
  
The 1.85.0 tag has been fixed.  
The beta tag should be attached to the last commit on master before the beta release - is that not the case?

---

## Comment 7

> Username: mclow  
> Created at: 2024-04-15 19:15:52 UTC  
> Url: https://github.com/boostorg/charconv/issues/175#issuecomment-2057629269  

The beta tag is indeed screwed up - I picked the wrong date. Will fix immediately.

---

## Comment 8

> Username: mborland  
> Created at: 2024-04-15 19:16:28 UTC  
> Url: https://github.com/boostorg/charconv/issues/175#issuecomment-2057630195  

> Dang - can't get anything right today :-(  
>   
> The 1.85.0 tag has been fixed. The beta tag should be attached to the last commit on master before the beta release - is that not the case?  
  
No it is well behind master (Tagged on commit from 12/11/2023). Scope seems to be in the same position.

---

## Comment 9

> Username: mclow  
> Created at: 2024-04-15 19:20:22 UTC  
> Url: https://github.com/boostorg/charconv/issues/175#issuecomment-2057636782  

Yeah. I did the same thing for scope - looked for the state as of the 1.84.0 release :-(  
It appears that the 1.85.0 and the 1.85.0.beta1 tag should refer to the same commit - and they do so now.

---

## Comment 10

> Username: mborland  
> Created at: 2024-04-15 19:24:24 UTC  
> Url: https://github.com/boostorg/charconv/issues/175#issuecomment-2057645049  

Looks good now. Thanks for the quick fix.

---

## Comment 11

> Username: mclow  
> Created at: 2024-04-15 19:25:27 UTC  
> Url: https://github.com/boostorg/charconv/issues/175#issuecomment-2057646717  

You're welcome. Sorry for the confusion.
