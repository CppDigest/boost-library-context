# #34 Visual studio support for <variant> property [Closed]

> Username: ENikS  
> Created at: 2014-09-04 15:58:35 UTC  
> Updated at: 2021-10-02 22:20:33 UTC  
> Closed at: 2014-10-07 07:20:20 UTC  
> Url: https://github.com/boostorg/build/pull/34  

Normally "variant" is either 'debug' or 'release' but VS only provides 'Debug' and 'Release'. I've added rule to support this capitalization as well so boost build could integrate better into MSVC scripting environment. This will provide 50% reduction in maintenance effort (no need to maintain two sets of project files).

---

## Comment 1

> Username: swatanabe  
> Created_at: 2014-09-04 16:18:09 UTC  
> Url: https://github.com/boostorg/build/pull/34#issuecomment-54503788  

AMDG  
  
On 09/04/2014 09:58 AM, Eugene Sadovoi wrote:  
  
> Normally &lt;variant&gt; is either &#39;debug&#39; or &#39;release&#39; but VS only provides &#39;Debug&#39; and &#39;Release&#39;. I&#39;ve added rule to support this capitalization as well so boost build could integrate better into MSVC scripting environment. This will provide 50% reduction in maintenance effort (no need to maintain two sets of project files).  
  
I don't really think this is a good idea.  
Release and release would still be treated  
as different properties and if they are  
both used (e.g. Release on the command line,  
and release in a Jamfile), it could cause  
serious issues, on a case insensitive filesystem.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: ENikS  
> Created_at: 2014-09-04 16:47:55 UTC  
> Updated_at: 2014-09-05 00:13:01 UTC  
> Url: https://github.com/boostorg/build/pull/34#issuecomment-54508180  

My rationale for the proposal as follows:  
- Properties are processed in following order: defaults, jam, command line (See jam.c). So later will override the previous. In other words even if both targets are updated it will end up with the same combination of set options: optimization: off, debug-symbols: on, inlining: off, runtime-debugging: on  
- Configurations provided for (v)ariant and (V)ariant are exactly the same so build criterias will not be changed.  
- This compatibility fix is not advertised or mentioned anywhere in documentation, online or run-time help so there is no issue of people starting to use it on other platforms or frameworks other than Visual Studio.  
  
Could you provide scenario where this fix could be a problem so I could test it more thoroughly?

---

## Comment 3

> Username: vprus  
> Created_at: 2014-09-08 12:09:11 UTC  
> Url: https://github.com/boostorg/build/pull/34#issuecomment-54808644  

Like Steven, I am not very keen on adding different spellings of features - it just seems to 'denormalize the schema' -- and such semantics was never meant, so we don't know what will break internally or for users.  
  
I actually don't understand the original motivation - but maybe, a composite feature called 'msvc-variant', with "Debug" and "Release" as values, expanding to <variant>debug and <variant>release, will be sufficient. Such a feature can be even project-specific, for initial testing.

---

## Comment 4

> Username: ENikS  
> Created_at: 2014-09-08 14:40:54 UTC  
> Updated_at: 2014-09-08 16:32:37 UTC  
> Url: https://github.com/boostorg/build/pull/34#issuecomment-54829389  

Well, anything that accepts Debug or Release will do but it have to be added on global level.   
  
I am working on MSBuild based deterministic build for bitcoin (original motivation) and project level solution will not work.   
Normally in ./Configure script you could specify variant as a variable containing debug/release. In MSBuild there is a variable $(Configuration) which holds either Debug of Release. There are no way to either lowercase it or use different variable so another set of configuration settings have to be created. Since bitcoin is rather dynamic project with a lot of changes every build cycle this maintenance effort becomes very taxing on time.   
Something like this:  
.\b2.exe toolset=msvc variant=$(Configuration) address-model=$(PlatformArchitecture) --build-dir=$(ProjectDir)obj\$(Platform)\$(Configuration) --stagedir=$(SolutionDir)packages\boost\build\native cxxflags="/wd4800 /wd4244 /wd4996 /wd4273 /wd4702 /wd4251"

---

## Comment 5

> Username: vprus  
> Created_at: 2014-09-13 07:32:07 UTC  
> Url: https://github.com/boostorg/build/pull/34#issuecomment-55484459  

> I am working on MSBuild based deterministic build for bitcoin (original motivation) and project level  
> solution will not work.   
  
Could you expand on that? If you're building a particular project (which is what you say above), then project level solution shall work. Or are you building some build service, or something?

---

## Comment 6

> Username: ENikS  
> Created_at: 2014-09-13 16:24:27 UTC  
> Url: https://github.com/boostorg/build/pull/34#issuecomment-55498984  

I am not sure what do you want me to expand on.   
I am unable to find a solution to convert 'Debug' into 'debug' withing VS project. There are no built in macro which would allow content of $(Configuration) to be lower cased.  
  
Perhaps this could be approached from different angle. What if attribute could be added to a "variant" declaration making it case insensitive? I am sure this could be used on more than one parameter. It may require minor code change but would allow greater flexibility.

---

## Comment 7

> Username: vprus  
> Created_at: 2014-09-13 18:07:03 UTC  
> Url: https://github.com/boostorg/build/pull/34#issuecomment-55501769  

If you want to build a single project, then what you can easily do is to modify its Jamroot to contain this:  
  
```  
import feature ;  
  
feature.feature vsvariant : Debug Release : composite ;  
  
feature.compose <vsvariant>Debug : <variant>debug ;  
feature.compose <vsvariant>Release : <variant>release ;  
```  
  
Then, running `b2 vsvariant=Release` will actually be equivalent to `b2 variant=release`. Is that what you're after?  
  
Note what with this solution, you'd have build directory with `vsvariant-Release` element, not `release` as normally would be the case. But then, if `variant=Release` is permitted, you'd have the same problem. Will such solution work for you?

---

## Comment 8

> Username: ENikS  
> Created_at: 2014-09-13 19:41:43 UTC  
> Updated_at: 2014-09-13 20:55:54 UTC  
> Url: https://github.com/boostorg/build/pull/34#issuecomment-55504554  

The main challenges with deterministic build are:   
- everything has to come from known sources   
- built without modifications  
  
So placing anything inside boost's root is problematic:  http://en.wikipedia.org/wiki/Deterministic_system or https://blog.torproject.org/blog/deterministic-builds-part-two-technical-details  
  
I will play with this suggestion and see if I could make it work. If not I guess I would keep two sets of settings for different variants.

---

## Comment 9

> Username: vprus  
> Created_at: 2014-10-07 07:20:20 UTC  
> Url: https://github.com/boostorg/build/pull/34#issuecomment-58146202  

Eugene,  
  
I am gonna mark this pull request as closed, since given the discussion above, I'm not comfortable merging it. I'd be happy to revisit when you've tried the suggestions.

---

## Comment 10

> Username: ENikS  
> Created_at: 2014-10-07 16:27:42 UTC  
> Updated_at: 2014-10-07 16:28:01 UTC  
> Url: https://github.com/boostorg/build/pull/34#issuecomment-58212930  

I found old dos script which could lowercase the variant. So, problem is solved. Thank you for your suggestions.

---
