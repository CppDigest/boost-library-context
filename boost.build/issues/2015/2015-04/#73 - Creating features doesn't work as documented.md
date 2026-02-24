# #73 - Creating features doesn't work as documented [Closed]

> Username: KayEss  
> Created at: 2015-04-26 05:53:52 UTC  
> Updated at: 2021-10-04 02:30:39 UTC  
> Closed at: 2021-10-04 02:30:39 UTC  
> Url: https://github.com/boostorg/build/issues/73  

I'm trying to create a feature to control the C++ version that I'm building with.  
  
I've started with the following:  
  
```  
import feature : feature ;  
feature cpp-version : cpp11 cpp14 :  
        composite propagated optional symmetric implicit  
    ;  
feature.compose <cpp-version>cpp11 :  
        <toolset>clang:<cxxflags>-std=c++11  
        <toolset>gcc:<cxxflags>-std=c++11  
    ;  
```  
  
One of `optional` or `symmetric` doesn't seem to work properly as without specifying `cpp11` on the command line the directory isn't included in the build path. In either case the extra compile flag isn't passed on to the compiler.  
  
If I change the feature to this:  
  
```  
import feature : feature ;  
feature cpp-version : cpp11 cpp14 :  
        composite propagated optional symmetric implicit  
    ;  
feature.compose <cpp-version>cpp11 :  
        <cxxflags>-std=c++11  
    ;  
```  
  
Then the compile flag is passed on if I specify `cpp11` on the command line, but the default still doesn't work. This will obviously break with compilers that specify this option differently.  
  
I'm going from the documentation here: http://www.boost.org/build/doc/html/bbv2/reference/definitions.html and here: http://www.boost.org/build/doc/html/bbv2/extending/features.html  
  
I'm using the current master of boost build.

---

## Comment 1

> Username: vprus  
> Created at: 2015-04-26 11:20:12 UTC  
> Url: https://github.com/boostorg/build/issues/73#issuecomment-96365933  

Kirit,  
  
Thanks for the report! I don't think we actually document anywhere that  
conditional requirements work as components of a composite feature - they  
don't. We probably should detect this and produce error message. An  
alternative, of course, is to make it supported, but:  
- it's not trivial,  
- it's probably not a good things for this feature - it's best if  
  individual tool sets determine what options to use when C++ 11 is requested  
  
What do you think?  
On Вс, 26 апр. 2015 г. at 8:54 Kirit Sælensminde notifications@github.com  
wrote:  
  
> I'm trying to create a feature to control the C++ version that I'm  
> building with.  
>   
> I've started with the following:  
>   
> import feature : feature ;  
> feature cpp-version : cpp11 cpp14 :  
>         composite propagated optional symmetric implicit  
>     ;  
> feature.compose <cpp-version>cpp11 :  
>         <toolset>clang:<cxxflags>-std=c++11  
>         <toolset>gcc:<cxxflags>-std=c++11  
>     ;  
>   
> One of optional or symmetric doesn't seem to work properly as without  
> specifying cpp11 on the command line the directory isn't included in the  
> build path. In either case the extra compile flag isn't passed on to the  
> compiler.  
>   
> If I change the feature to this:  
>   
> import feature : feature ;  
> feature cpp-version : cpp11 cpp14 :  
>         composite propagated optional symmetric implicit  
>     ;  
> feature.compose <cpp-version>cpp11 :  
>         <cxxflags>-std=c++11  
>     ;  
>   
> Then the compile flag is passed on if I specify cpp11 on the command  
> line, but the default still doesn't work. This will obviously break with  
> compilers that specify this option differently.  
>   
> I'm going from the documentation here:  
> http://www.boost.org/build/doc/html/bbv2/reference/definitions.html and  
> here: http://www.boost.org/build/doc/html/bbv2/extending/features.html  
>   
> I'm using the current master of boost build.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/build/issues/73.

---

## Comment 2

> Username: KayEss  
> Created at: 2015-04-27 04:47:42 UTC  
> Url: https://github.com/boostorg/build/issues/73#issuecomment-96502967  

What I think you're saying is that it isn't really possible to do the sort of thing that I'm trying without altering the core of Boost build itself. Ok, that's fair enough -- an error message would be nice though.  
  
I still don't understand what's up with the `symmetric` and `optional` parts though -- those also don't appear to work as the documentation implies. Maybe some sort of table showing which options are compatible with which others, or error messages for invalid combinations rather than just having them ignored?  
  
What I really think is missing are some documented examples. The first one on this page http://www.boost.org/build/doc/html/bbv2/extending/features.html is difficult to understand. Are those three blocks in the same file or different ones? What is `verbatim.inline-file`? It's a free feature so what happens when it's specified multiple times? Is that already taken care of in the mapping to `OPTIONS`? `actions` isn't listed here http://www.boost.org/build/doc/html/bbv2/reference/rules.html what is it, and what does it do?  
  
The example further down the page also seems to skip some things. Step 2 says you need to add a line to MSVC -- which file does that line go into? I assume that I need to find the msvc.jam file somewhere in Boost build. Does it matter where in the file the line goes?  
  
I have written some extensions that do the work that I want and I'm happy to try to find the time to write them up -- I probably ought to document them anyway. Before I do write them up though it'd be good to do a quick review just so that I know that they're done properly -- are you amenable to doing the review? I don't think any one of the examples should be very big -- probably just a line or two to start with.  
  
How would you want to go about this? I can write them in a page on the wiki here, do them through an issue, add an example file to boost build itself and we can review through a pull request or I can write them up on my blog. Whatever works for you.

---

## Comment 3

> Username: vprus  
> Created at: 2015-04-27 16:16:29 UTC  
> Url: https://github.com/boostorg/build/issues/73#issuecomment-96728948  

Thanks for the offer - I agree this needs improvement. Which approach is easiest for you? Pull request will allow to comment on individual lines easily, while wiki page is probably less work, while blog post is more of publicity. Feel free to pick anything.
