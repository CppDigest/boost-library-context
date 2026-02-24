# #201 RFC: Remove library aliases in Jamroot? [Closed]

> Username: pdimov  
> Created at: 2018-10-05 21:43:49 UTC  
> Updated at: 2018-10-05 23:28:35 UTC  
> Closed at: 2018-10-05 23:28:27 UTC  
> Url: https://github.com/boostorg/boost/pull/201  

This is less of a pull request than a request for comments. Do we need these library aliases in Jamroot? How are they useful? Issuing f.ex. `b2 system` would indeed build System, but will leave it in `bin.v2`, and what's the use of that?  
  
It might be useful to have `stage-$lib` and `install-$lib` targets that alias to `libs/$lib/build//stage` and `libs/$lib/build//install`, but these? Can't think of why we have them, although I'm sure there was a reason for that, and maybe there still is.

---

## Comment 1

> Username: swatanabe  
> Created_at: 2018-10-05 21:53:19 UTC  
> Url: https://github.com/boostorg/boost/pull/201#issuecomment-427509035  

AMDG  
  
On 10/05/2018 03:43 PM, Peter Dimov wrote:  
> This is less of a pull request than a request for comments. Do we need these library aliases in Jamroot? How are they useful? Issuing f.ex. `b2 system` would indeed build System, but will leave it in `bin.v2`, and what's the use of that?  
>   
> It might be useful to have `stage-$lib` and `install-$lib` targets that alias to `libs/$lib/build//stage` and `libs/$lib/build//install`, but these? Can't think of why we have them, although I'm sure there was a reason for that, and maybe there still is.  
  
You can't just remove them.  They're used quite a lot internally, e.g.  
  
run test.cpp /boost//unit_test_framework ;  
  
(That's what I use, at least.)  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-10-05 22:01:28 UTC  
> Url: https://github.com/boostorg/boost/pull/201#issuecomment-427510545  

Yes, that's also what @mjcaisse said on Slack. I thought that the idiomatic reference was `/boost/test//unit_test_framework` - the part enabled by the later  
  
```  
# Make project ids of all libraries known.  
for local l in $(all-libraries)  
{  
    use-project /boost/$(l) : libs/$(l)/build ;  
}  
```  
  
part - but if these are in common use, we can't remove them.  
  
I'll leave this open only because I'm curious what Travis will say about it, then I'll close it. :-)

---

## Comment 3

> Username: swatanabe  
> Created_at: 2018-10-05 22:11:52 UTC  
> Url: https://github.com/boostorg/boost/pull/201#issuecomment-427512539  

AMDG  
  
On 10/05/2018 04:01 PM, Peter Dimov wrote:  
> Yes, that's also what @mjcaisse said on Slack. I thought that the idiomatic reference was `/boost/test//unit_test_framework` - the part enabled by the later  
>   
  
It's actually /boost/test//boost_unit_test_framework.  
The only reason I use the alias is because it's shorter.  
Also, boost.jam (The Boost.Build script for importing  
pre-installed Boost) uses these aliases.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: pdimov  
> Created_at: 2018-10-05 22:16:49 UTC  
> Url: https://github.com/boostorg/boost/pull/201#issuecomment-427513370  

`boost.jam`?  
  
There's something to be said about the need to maintain a list of aliases here in Jamroot. It's now missing the various `stacktrace_*`s for instance. Perhaps the aliasing process needs to be done in `boost-install` along with other things such as the CMake config. :-)

---

## Comment 5

> Username: swatanabe  
> Created_at: 2018-10-05 22:33:38 UTC  
> Url: https://github.com/boostorg/boost/pull/201#issuecomment-427516261  

AMDG  
  
On 10/05/2018 04:16 PM, Peter Dimov wrote:  
> `boost.jam`?  
>   
  
tools/build/src/contrib/boost.jam  
(See `b2 --help boost`)  
  
> There's something to be said about the need to maintain a list of aliases here in Jamroot.  
  
There is.  I'm just pointing out issues that would  
need to be handled to make this change viable.  
  
> It's now missing the various `stacktrace_*`s for instance. Perhaps the aliasing process needs to be done in `boost-install` along with other things such as the CMake config. :-)  
>   
  
That won't work.  The aliases need to be set up when Jamroot  
is loaded, not when the library specific Jamfile is loaded.  
  
In Christ,  
Steven Watanabe

---
