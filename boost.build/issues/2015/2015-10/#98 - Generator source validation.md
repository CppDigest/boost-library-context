# #98 - Generator source validation? [Open]

> Username: frenchtoast747  
> Created at: 2015-10-01 17:37:12 UTC  
> Updated at: 2015-10-01 19:17:08 UTC  
> Url: https://github.com/boostorg/build/issues/98  

The following scenario occurred (simplified for brevity):  
  
```  
# in a jamfile  
alias MY_ALIAS ;  
lib LIB : MY_ALIAS ;  
```  
  
Then, trying to build the `LIB` target:  
  
```  
*** argument error  
* rule class@archive-generator.run ( project name ? : property-set : sources + )  
* called with: ( object(project-target)@3272 LIB : object(property-set)@7510 :  )  
* missing argument sources  
```  
  
The source for the `LIB` target ends up producing an empty source list (user error). I can clearly tell that has happened from the error message, but I can't tell _where_ it occurred.  
  
**Note**: we use the convention of specifying a single `LIB` target per library and in a project that uses multiple libraries, there are multiple `LIB` targets which is why it's not entirely clear _which_ `LIB` target  the error message is referring to.  
  
I have a couple of ideas that could be used to help the end user solve this problem.   
1. For the base generator class, the `run` signature could be updated to use `*` instead of `+` and then check to see if `sources` is empty and print a better error message (target name, location, etc).  
2. The Jam interpreter and the class implementations could be modified to support a "to string" or "repr" method and those would be called in the above error message. This would also be a benefit in several other places like `--debug-building` and `--debug-generators`.  
  
The problem with the second idea is that it doesn't help the Python port.  
  
Thoughts?

---

## Comment 1

> Username: vprus  
> Created at: 2015-10-01 19:17:08 UTC  
> Url: https://github.com/boostorg/build/issues/98#issuecomment-144821276  

I thought that in Python port, we'd do errors.push-user-conext in  
BasicTarget.generate and so the backtrace will report the metatarget being  
generated. This is from memory thought - don't have a computer right now,  
will check later.  
  
On Thu, Oct 1, 2015, 20:37 Aaron Boman notifications@github.com wrote:  
  
> The following scenario occurred (simplified for brevity):  
>   
> # in a jamfile  
>   
> alias MY_ALIAS ;  
> lib LIB : MY_ALIAS ;  
>   
> Then, trying to build the LIB target:  
>   
> **\* argument error  
> - rule class@archive-generator.run ( project name ? : property-set : sources + )  
> - called with: ( object(project-target)@3272 LIB : object(property-set)@7510 :  )  
> - missing argument sources  
>   
> The source for the LIB target ends up producing an empty source list  
> (user error). I can clearly tell that has happened from the error message,  
> but I can't tell _where_ it occurred.  
>   
> _Note_: we use the convention of specifying a single LIB target per  
> library and in a project that uses multiple libraries, there are multiple  
> LIB targets which is why it's not entirely clear _which_ LIB target the  
> error message is referring to.  
>   
> I have a couple of ideas that could be used to help the end user solve  
> this problem.  
> 1. For the base generator class, the run signature could be updated to  
>    use \* instead of + and then check to see if sources is empty and print  
>    a better error message (target name, location, etc).  
> 2. The Jam interpreter and the class implementations could be modified  
>    to support a "to string" or "repr" method and those would be called in the  
>    above error message. This would also be a benefit in several other places  
>    like --debug-building and --debug-generators.  
>   
> The problem with the second idea is that it doesn't help the Python port.  
>   
> Thoughts?  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/build/issues/98.
