# #43 - Add CI builds to improve pull request and code quality [Closed]

> Username: jeking3  
> Created at: 2018-03-06 16:06:12 UTC  
> Updated at: 2018-03-11 22:26:57 UTC  
> Closed at: 2018-03-11 22:26:57 UTC  
> Url: https://github.com/boostorg/program_options/issues/43  

CI builds would run every pull request and periodically run master and develop through builds to prove out code correctness (i.e. does it compile, link, and pass unit tests?) as well as code coverage and static code analysis (coverity scan, cppcheck, ubsan jobs).  
  
There are good examples in the Boost.DateTime, Boost.Format, and Boost.Uuid repositories on how to set these up.  I can assist in setting these up if needed.

---

## Comment 1

> Username: vprus  
> Created at: 2018-03-07 09:56:26 UTC  
> Url: https://github.com/boostorg/program_options/issues/43#issuecomment-371085556  

CI is an obviously good idea, and if you provide a PR that adds support I'd be happy to merge it. (Personally, I lost track which of the multiple solutions is considered best for Boost)

---

## Comment 2

> Username: jeking3  
> Created at: 2018-03-07 12:03:01 UTC  
> Url: https://github.com/boostorg/program_options/issues/43#issuecomment-371117448  

One (PR) is forthcoming.  Should be today.  
  
Also, there are 90 open issues in Boost Trac going back to version 1.40.0 that need to be scrubbed.  Based on my experience with other repositories, chances are that a good percentage are already fixed.  https://svn.boost.org/trac10/query?status=!closed&component=program_options
