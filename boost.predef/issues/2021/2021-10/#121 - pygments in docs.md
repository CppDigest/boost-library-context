# #121 - pygments in docs [Closed]

> Username: sdarwin  
> Created at: 2021-10-21 17:11:18 UTC  
> Updated at: 2021-10-24 22:55:14 UTC  
> Closed at: 2021-10-24 22:55:14 UTC  
> Url: https://github.com/boostorg/predef/issues/121  

The predef docs use pygments syntax hightlighting in conjunction with asciidoctor.  
  
Here's a quote from https://github.com/asciidoctor/docker-asciidoctor/pull/63  
"At this point, we're recommending against the use of pygments as the source highlighter in Asciidoctor. Rouge is now supported by default in the major converters and doesn't rely on a system command or separate language runtime."  
  
When upgrading the boost_superproject_build docker image to use python3, there are pygments errors.  
  
```  
/opt/github/boost/libs/predef/doc/pygments_init.rb:20:in `<top (required)>': undefined method `create' for Pygments::Lexer:Class (NoMethodError)  
```  
  
In 2020, pygments.rb didn't officially support python3. At that time, I tried an unofficial version from a user's forked repository, and was able to get that to build, along with predef.    
  
In 2021, supposedly https://github.com/pygments/pygments.rb v2.2.0 does officially support python3.  Yet, the predef build is broken.  The method "create" has been removed.   I will ask about it in the pygments repo.  
  
Proposed solution:  
  
Overall, would it be better to switch from pygments to rouge?   Then all such issues are avoided.

---

## Comment 1

> Username: sdarwin  
> Created at: 2021-10-21 19:41:40 UTC  
> Updated at: 2021-10-21 19:41:54 UTC  
> Url: https://github.com/boostorg/predef/issues/121#issuecomment-948946375  

The most recent version of pygments.rb that still defines the method 'create' is 2.1.0.    That can be a work-around, until a fix is decided.      
If pygments_init.rb will be updated, it should check on presence or absence of the method, thus supporting both/either older or newer pygments.rb

---

## Comment 2

> Username: grafikrobot  
> Created at: 2021-10-21 21:29:00 UTC  
> Url: https://github.com/boostorg/predef/issues/121#issuecomment-949017239  

I'll change to rouge. It was already on my list of things to do. Will take me a couple of days to get that and other changes done though.

---

## Comment 3

> Username: grafikrobot  
> Created at: 2021-10-24 22:55:14 UTC  
> Url: https://github.com/boostorg/predef/issues/121#issuecomment-950409760  

Fixed in https://github.com/boostorg/predef/commit/a12c7fde140dccc8dd1e052bfd07689fffdcfb23
