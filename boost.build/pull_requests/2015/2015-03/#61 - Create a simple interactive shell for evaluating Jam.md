# #61 Create a simple interactive shell for evaluating Jam. [Closed]

> Username: frenchtoast747  
> Created at: 2015-03-08 13:44:20 UTC  
> Updated at: 2021-10-02 22:20:34 UTC  
> Closed at: 2015-03-14 22:40:25 UTC  
> Url: https://github.com/boostorg/build/pull/61  

One of the biggest complaints I get about using Boost.Build is that Jam is confusing. There isn't any sort of a sandbox to play around in with the language itself. The best solution I've come up with is throwing all the  statements I want to eval at the beginning of my Jamroot immediately followed by an `exit ;`.  
  
One of the best resources at my disposal while learning Python was the interactive shell. So, I've created something similar to automating the above solution. In the long run, I'd like to actually use the internal parser, but it's so tightly integrated with the compile phase that I just opted with this stupid simple solution for now.  
  
To use, either directly run `interpreter.py` (`-e` to specify where the `b2` executable is located if it's not in your path) or run `b2 --python --interactive` to bring up the shell. Then, issue simple Jam statements.  
  
An example session  
  
```  
Boost.Build Interactive Shell  
Type "help ;" for a simple tutorial.  
________________________________________________________________________________  
>>> # simple variable declaration  
>>> x = asdf fdsa qwerty ;  
>>> # simple variable evaluation;  
>>> # the user doesn't have to manually "echo" everything  
>>> $(x) ;  
asdf fdsa qwerty  
>>> y = 1 2 3 ;  
>>> # expected expansions  
>>> $(x)::$(y) ;  
asdf::1 asdf::2 asdf::3 fdsa::1 fdsa::2 fdsa::3 qwerty::1 qwerty::2 qwerty::3  
>>> # rule declarations  
>>> rule say-hello ( name ? )  
... {  
...     name ?= there ;  
...     echo Hello, $(name). ;  
... }  
>>> # the rules are remembered and can be invoked  
>>> say-hello Aaron ;  
Hello, Aaron.  
>>> x = prefix/$(x) ;  
>>> # it's possible to import another module on the BOOST_BUILD_PATH  
>>> import regex ;  
>>> # rules can be evaluated without an echo too  
>>> [ regex.replace-list $(x) : prefix/ : new-prefix/ ] ;  
new-prefix/asdf new-prefix/fdsa new-prefix/qwerty  
```

---

## Comment 1

> Username: swatanabe  
> Created_at: 2015-03-13 23:06:13 UTC  
> Url: https://github.com/boostorg/build/pull/61#issuecomment-79517565  

AMDG  
  
  This is a good idea, in theory.  In the  
long term, I want to implement a debugger  
as part of b2.  The main issue I have is  
that it's basically impossible to make the  
system of saving statements and re-executing  
them handle all situations.  What you really  
need to do is to compile and run each statement  
in a single module.  This means adding a new  
builtin rule, which would be implemented like:  
- Set up the lexer to read from the string  
- call the parser  
- Strip off local from the top level node  
- wrap the AST in a call to echo  
- call function_compile  
- call function_run  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: frenchtoast747  
> Created_at: 2015-03-14 22:40:25 UTC  
> Url: https://github.com/boostorg/build/pull/61#issuecomment-80746356  

Agreed. In that case, I'll close this pull request so that the correct solution can be implemented. Thanks!

---

## Comment 3

> Username: KayEss  
> Created_at: 2015-03-16 05:21:53 UTC  
> Url: https://github.com/boostorg/build/pull/61#issuecomment-81425693  

Does this not help people in the meantime, or is the concern that doing this will block out a better approach?

---

## Comment 4

> Username: frenchtoast747  
> Created_at: 2015-03-16 14:08:56 UTC  
> Updated_at: 2015-03-16 18:06:03 UTC  
> Url: https://github.com/boostorg/build/pull/61#issuecomment-81687236  

In theory, after the better solution is implemented, there should only need to be two changes. There will be a need for more initialization code and a switch from calling the subprocess to `b2` to just calling the new builtin rule. So, there shouldn't be a block for the better approach, once implemented.

---
