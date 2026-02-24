# #199 - "Invalid range end in character class" for dash after character range [Open]

> Username: cai-lw  
> Created at: 2023-11-01 02:13:31 UTC  
> Updated at: 2024-11-25 13:59:56 UTC  
> Url: https://github.com/boostorg/regex/issues/199  

## Summary  
For most other regex engines, `[X-Y-Z]` is a valid character set, consisting of character range X to Y, literal dash `-`, and character Z. For example, you can verify at https://regex101.com/ that this is valid for all regex flavors it supports.  
  
Currently Boost rejects this under the default syntax. It's not clear from the [documentation](https://www.boost.org/doc/libs/1_83_0/libs/regex/doc/html/boost_regex/syntax/perl_syntax.html) whether this is valid for Boost. This can be easily fixed by e.g. changing to `[X-YZ-]` but I'm still interested in knowing if rejecting this is intentional.  
  
I encountered this when migrating from `std::regex`. This is explicitly valid for `std::regex`:  
https://en.cppreference.com/w/cpp/regex/ecmascript:  
> The character - is treated literally if it is  
> * immediately follows a dash-separated range specification.  
  
## Minimal reproducible example  
### Code  
```  
#include <boost/regex.hpp>  
  
int main() {  
    const boost::regex re("[0-9-#]+");  
    return boost::regex_match("12#34-56", re);  
}  
```  
### Expected behavior  
Returns 1  
  
### Actual behavior  
```  
terminate called after throwing an instance of 'boost::wrapexcept<boost::regex_error>'  
  what():  Invalid range end in character class  The error occurred while parsing the regular expression: '[0-9->>>HERE>>>#]+'.  
Program terminated with signal: SIGSEGV  
```  
  
## Proposed fixes  
* Determine whether we intend to accept or reject such syntax.  
* If yes, change parsing code accordingly.  
* In either case, clarify in the documentation

---

## Comment 1

> Username: JohnAlt2  
> Created at: 2024-11-03 00:19:09 UTC  
> Updated at: 2024-11-25 13:59:56 UTC  
> Url: https://github.com/boostorg/regex/issues/199#issuecomment-2453236298  

Does not answer whether should differ from other flavours or not. And I can no longer find documentation of Boost behaviour in this. But I can say what current,  and I believe designed behaviour is:  
○ Literal "-" must be escaped "\\-" unless the 1st or last character in the class e.g. "[-DEF]" or "[DEF-]" as you state yourself.  
  
I would advise that good coding standard is to almost never use an ambiguous sequence,  even when allowed by the interpreter or compiler, if there is a more explicit alternative. For example we always use "[\\-DEF]" style, so that if there is an edit that extends the class, e.g. "[123\\-DEF]", it can not unintentionally create a class range. Similar with your example "[X-Y-Z]" could get extended to include "A" i.e. "[X-YA-Z]" creating a whole alphabet class by accident. Explicitness also makes it easier for another coder to read and understand your code.  
{above explanation should now more sense now that I have escaped the previously invisible slashes "\\" so that they are now displayed **LOL**}

---

## Comment 2

> Username: cai-lw  
> Created at: 2024-11-23 01:04:37 UTC  
> Updated at: 2024-11-23 01:05:23 UTC  
> Url: https://github.com/boostorg/regex/issues/199#issuecomment-2495171797  

I agree that no one should write regexes like that and I'm totally fine with Boost rejecting such regexes. The question is more about whether this is a deliberate design and whether this needs to be documented.
