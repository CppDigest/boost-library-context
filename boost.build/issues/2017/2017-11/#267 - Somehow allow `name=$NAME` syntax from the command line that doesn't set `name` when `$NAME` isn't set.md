# #267 - Somehow allow `name=$NAME` syntax from the command line that doesn't set `name` when `$NAME` isn't set? [Closed]

> Username: pdimov  
> Created at: 2017-11-28 17:27:39 UTC  
> Updated at: 2018-01-09 01:12:57 UTC  
> Closed at: 2018-01-09 01:10:18 UTC  
> Url: https://github.com/boostorg/build/issues/267  

It's often the case in f.ex. Travis and Appveyor scripts that one has an environment variable `CXXSTD` (for instance) that can be set to, say, `03,11`, in which case one wants `cxxstd=03,11` on the `b2` command line, or it can not be set, in which case one wants no `cxxstd` at all, rather that `cxxstd=`, which doesn't work.  
  
Does anyone have any ideas what is the most elegant way to accomplish this? Yes, one can use `CXXSTD=cxxstd=03,11`, but that's inelegant. :-)  
  
Perhaps a syntax along the lines of `cxxstd?=$CXXSTD` on the command line could be added?

---

## Comment 1

> Username: swatanabe  
> Created at: 2017-12-05 19:38:57 UTC  
> Url: https://github.com/boostorg/build/issues/267#issuecomment-349417155  

AMDG  
  
On 11/28/2017 10:27 AM, Peter Dimov wrote:  
> It's often the case in f.ex. Travis and Appveyor scripts that one has an environment variable `CXXSTD` (for instance) that can be set to, say, `03,11`, in which case one wants `cxxstd=03,11` on the `b2` command line, or it can not be set, in which case one wants no `cxxstd` at all, rather that `cxxstd=`, which doesn't work.  
>   
> Does anyone have any ideas what is the most elegant way to accomplish this? Yes, one can use `CXXSTD=cxxstd=03,11`, but that's inelegant. :-)  
>   
> Perhaps a syntax along the lines of `cxxstd?=$CXXSTD` on the command line could be added?  
>   
  
I think this is something better left to the shell:  
  
bash: ${CXXSTD:+cxxstd=${CXXSTD}}  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: pdimov  
> Created at: 2017-12-05 19:54:33 UTC  
> Updated at: 2017-12-05 19:55:05 UTC  
> Url: https://github.com/boostorg/build/issues/267#issuecomment-349421689  

Thanks Steven. Unfortunately, this won't work on Appveyor. :-)  
  
Is there not a need in b2 for the ability to remove a property without knowing its previous value? If you inherit `<cxxstd>11` you can remove that with `-<cxxstd>11`, but not if you don't know what it was. If `<cxxstd>` (without a value) worked, `cxxstd=` from the command line would work as well.

---

## Comment 3

> Username: pdimov  
> Created at: 2017-12-05 19:55:23 UTC  
> Url: https://github.com/boostorg/build/issues/267#issuecomment-349421898  

(edit: `cxxflags` -> `cxxstd`)

---

## Comment 4

> Username: swatanabe  
> Created at: 2017-12-05 22:13:02 UTC  
> Url: https://github.com/boostorg/build/issues/267#issuecomment-349459158  

AMDG  
  
On 12/05/2017 12:54 PM, Peter Dimov wrote:  
> Thanks Steven. Unfortunately, this won't work on Appveyor. :-)  
>   
  
  Well, what do you expect from cmd.exe?  
if not "%CXXSTD%" == "" set CXXSTD=cxxstd=%CXXSTD%  
  
> Is there not a need in b2 for the ability to remove a property without knowing its previous value?  
> If you inherit `<cxxflags>11` you can remove that with `-<cxxflags>11`, but not if you don't know what it was. If `<cxxflags>` (without a value) worked, `cxxflags=` from the command line would work as well.  
>   
  
  Allowing it in general is problematic, because  
it can lead to inconsistent state for features  
that require a value.  The -property syntax is  
very fragile and only works in very limited contexts.  
I guess it would make some sense to allow `<feature>`  
and `feature=` for optional features only.  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: pdimov  
> Created at: 2017-12-24 03:16:02 UTC  
> Url: https://github.com/boostorg/build/issues/267#issuecomment-353762732  

It occurs to me that allowing an empty value for optional features as an alias for not present could allow `cxxstd=,03,11`, which would expand to "", "`<cxxstd>03`", "`<cxxstd>11`". This is at present impossible to achieve in a single invocation.

---

## Comment 6

> Username: swatanabe  
> Created at: 2017-12-24 14:19:37 UTC  
> Url: https://github.com/boostorg/build/issues/267#issuecomment-353786844  

AMDG  
  
On 12/23/2017 08:16 PM, Peter Dimov wrote:  
> It occurs to me that allowing an empty value for optional features as an alias for not present could allow `cxxstd=,03,11`, which would expand to "", "`<cxxstd>03`", "`<cxxstd>11`". This is at present impossible to achieve in a single invocation.  
>   
  
Also, there's precedent for this in toolset.flags:  
  
# taken from gcc.jam:  
toolset.flags gcc OPTIONS  
<architecture>x86/<address-model>32/<instruction-set> : -march=i686 ;  
  
In Christ,  
Steven Watanabe

---

## Comment 7

> Username: swatanabe  
> Created at: 2018-01-09 00:49:20 UTC  
> Url: https://github.com/boostorg/build/issues/267#issuecomment-356144303  

So, I just got around to this and when I tried 'cxxstd=,03' it worked (modulo slightly funky paths).  What errors do you get?

---

## Comment 8

> Username: swatanabe  
> Created at: 2018-01-09 00:53:34 UTC  
> Url: https://github.com/boostorg/build/issues/267#issuecomment-356144920  

Oh, I see.  I accidentally made it work in 12decb3ce680031b915f69902795eec47224fc7d.

---

## Comment 9

> Username: swatanabe  
> Created at: 2018-01-09 01:10:17 UTC  
> Url: https://github.com/boostorg/build/issues/267#issuecomment-356147907  

It appears that this was supposed to work in the first place, and the only reason it failed was because the implementation of cxxstd was done in a non-idiomatic way.  Support for explicitly setting the value of an optional feature to empty is a bit odd in some ways, as it's not quite equivalent to not setting it at all, but I don't really want to change it as it could have surprising consequences.  For example, currently a default-build will be applied if an optional feature is missing, but not if it is explicitly set to empty.  This is the most intuitive behavior for this case.

---

## Comment 10

> Username: pdimov  
> Created at: 2018-01-09 01:12:57 UTC  
> Url: https://github.com/boostorg/build/issues/267#issuecomment-356148301  

Yes, I just tried it with `toolset=msvc-14.1 cxxstd=,14,17` and it worked there too. Good enough for me, as long as it stays working. :-) Thanks for looking into it.
