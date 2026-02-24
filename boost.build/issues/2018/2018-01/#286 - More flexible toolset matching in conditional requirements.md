# #286 - More flexible toolset matching in conditional requirements? [Open]

> Username: pdimov  
> Created at: 2018-01-18 13:12:41 UTC  
> Updated at: 2021-06-26 03:10:24 UTC  
> Url: https://github.com/boostorg/build/issues/286  

Conditional toolset requirements require an exact match, f.ex. `<toolset>gcc-4.4.7:<build>no`, but this is quite inconvenient as the full names are often illogical (the autodetected ones on Travis are `gcc-4.4.7`, `gcc-4.6`, `gcc-4.7`, `gcc-4.8` and then `gcc-4.9.4` again for some reason) and subject to constant change after minor upgrades. And the names on the test matrix are really creative.  
  
Would it be possible to have a way to match against f.ex. `gcc` version 4.anything, or 4.4.anything? Even a simple prefix match (`gcc-4.4*`, `gcc-5*`) would work well in practice. (In principle `name-1*` would match both version 1 and version 14, but we don't have any such.)

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-01-18 14:29:52 UTC  
> Url: https://github.com/boostorg/build/issues/286#issuecomment-358663068  

AMDG  
  
On 01/18/2018 06:12 AM, Peter Dimov wrote:  
> Conditional toolset requirements require an exact match, f.ex. `<toolset>gcc-4.4.7:<build>no`, but this is quite inconvenient as the full names are often illogical (the autodetected ones on Travis are `gcc-4.4.7`, `gcc-4.6`, `gcc-4.7`, `gcc-4.8` and then `gcc-4.9.4` again for some reason) and subject to constant change after minor upgrades. And the names on the test matrix are really creative.  
>   
  
See also: https://github.com/boostorg/build/issues/253  
  
> Would it be possible to have a way to match against f.ex. `gcc` version 4.anything, or 4.4.anything? Even a simple prefix match (`gcc-4.4*`, `gcc-5*`) would work well in practice. (In principle `name-1*` would match both version 1 and version 14, but we don't have any such.)  
>   
  
  It's not exactly easy to implement, because the  
toolset might have other subfeatures besides the  
version, e.g. <toolset>clang-3.8.0 will match both  
clang-darwin-3.9.0 and clang-linux-3.9.0.  
  
You can do it with an indirect conditional now:  
  
rule check-gcc ( properties * )  
{  
    switch [ feature.get-values <toolset-gcc:version>  : $(properties) ]  
    {  
        case 4.* : return <build>no ;  
    }  
}  
  
...  
<conditonal>@check-gcc  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: pdimov  
> Created at: 2018-01-18 14:53:18 UTC  
> Url: https://github.com/boostorg/build/issues/286#issuecomment-358669940  

With respect to not easy, yes, I haven't been able to even figure out how it works. I can see the code that evaluates the conditional requirements: https://github.com/boostorg/build/blob/develop/src/build/property.jam#L90  
  
and I see where it's used: https://github.com/boostorg/build/blob/develop/src/build/targets.jam#L1082  
  
but I still don't see how `<toolset>clang-3.8.0:<build>no` is supposed to match.

---

## Comment 3

> Username: swatanabe  
> Created at: 2018-01-18 15:20:00 UTC  
> Url: https://github.com/boostorg/build/issues/286#issuecomment-358678685  

AMDG  
  
On 01/18/2018 07:53 AM, Peter Dimov wrote:  
> With respect to not easy, yes, I haven't been able to even figure out how it works. I can see the code that evaluates the conditional requirements: https://github.com/boostorg/build/blob/develop/src/build/property.jam#L90  
>   
> and I see where it's used: https://github.com/boostorg/build/blob/develop/src/build/targets.jam#L1082  
>   
> but I still don't see how `<toolset>clang-3.8.0:<build>no` is supposed to match.  
>   
  
It matches because it's expanded to  
<toolset>clang,<toolset-clang:version>3.8.0:<build>no  
by feature.expand-subfeatures-in-conditions.  
  
  I was originally thinking that this would have  
to be totally redone, since a pattern could match  
multiple subfeature values and a conditional can't  
represent 'or' constaints, but then I realized that  
there's nothing stopping us from returning multiple  
properties, so <toolset>gcc-4.*:<build>no would become  
<toolset>gcc,<toolset-gcc:version>4.4.0:<build>no.  
<toolset>gcc,<toolset-gcc:version>4.5.1:<build>no  
(or similar) based on what subfeature values are known.  
I think this is doable, provided we can agree on  
precisely how the pattern is specified and make sure  
that it isn't ambiguous.  (Boost.Build already has  
way too many ad hoc special rules for interpreting  
properties.)  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: grafikrobot  
> Created at: 2018-01-18 15:28:14 UTC  
> Url: https://github.com/boostorg/build/issues/286#issuecomment-358681289  

On Thu, Jan 18, 2018 at 9:20 AM, swatanabe <notifications@github.com> wrote:  
  
> AMDG  
>  
> On 01/18/2018 07:53 AM, Peter Dimov wrote:  
> > With respect to not easy, yes, I haven't been able to even figure out  
> how it works. I can see the code that evaluates the conditional  
> requirements: https://github.com/boostorg/build/blob/develop/src/build/  
> property.jam#L90  
> >  
> > and I see where it's used: https://github.com/boostorg/  
> build/blob/develop/src/build/targets.jam#L1082  
> >  
> > but I still don't see how `<toolset>clang-3.8.0:<build>no` is supposed  
> to match.  
> >  
>  
> It matches because it's expanded to  
> <toolset>clang,<toolset-clang:version>3.8.0:<build>no  
> by feature.expand-subfeatures-in-conditions.  
>  
> I was originally thinking that this would have  
> to be totally redone, since a pattern could match  
> multiple subfeature values and a conditional can't  
> represent 'or' constaints, but then I realized that  
> there's nothing stopping us from returning multiple  
> properties, so <toolset>gcc-4.*:<build>no would become  
> <toolset>gcc,<toolset-gcc:version>4.4.0:<build>no.  
> <toolset>gcc,<toolset-gcc:version>4.5.1:<build>no  
> (or similar) based on what subfeature values are known.  
> I think this is doable, provided we can agree on  
> precisely how the pattern is specified and make sure  
> that it isn't ambiguous. (Boost.Build already has  
> way too many ad hoc special rules for interpreting  
> properties.)  
>  
  
An alternative is to create a new feature attribute to handle "version"  
numbers specifically. But that might be more work.  
  
  
--   
-- Rene Rivera  
-- Grafik - Don't Assume Anything  
-- Robot Dreams - http://robot-dreams.net

---

## Comment 5

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:45 UTC  
> Url: https://github.com/boostorg/build/issues/286#issuecomment-868936522  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
