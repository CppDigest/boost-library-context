# #65 - Cannot figure out how to call qualified rule [Open]

> Username: grisumbras  
> Created at: 2015-03-20 12:52:36 UTC  
> Updated at: 2015-03-24 15:40:24 UTC  
> Url: https://github.com/boostorg/build/issues/65  

As Boost Build manual suggests I ask this question inside an issue.  
I recently started to study Boost Build and I like how it does things a lot. But when I reached [the section about modules](http://www.boost.org/build/doc/html/jam/language.html#jam.language.modules) and copy-pasted the first example inside a project definition, b2 exited with an error.  
  
Here is the full Jamroot contents:  
  
```  
project someproject ;  
  
module my_module  
{  
    rule salute ( x ) { ECHO $(x), world ; }  
    rule greet ( ) { salute hello ; }  
    greet ;  
}  
my_module.salute goodbye ;  
```  
  
The output is:  
  
```  
hello, world  
Jamroot:12: in modules.load  
ERROR: rule "my_module.salute" unknown in module "Jamfile</home/grisumbras/Documents/projects/someproject>".  
/usr/share/boost-build/build/project.jam:311: in load-jamfile  
/usr/share/boost-build/build/project.jam:64: in load  
/usr/share/boost-build/build/project.jam:145: in project.find  
/usr/share/boost-build/build-system.jam:535: in load  
/usr/share/boost-build/kernel/modules.jam:289: in import  
/usr/share/boost-build/kernel/bootstrap.jam:139: in boost-build  
/usr/share/boost-build/boost-build.jam:8: in module scope  
```  
  
`b2 -v` produces `Boost.Jam  Version 2014.03. OS=LINUX.`  
  
So, how a rule in a module should be invoked?

---

## Comment 1

> Username: grisumbras  
> Created at: 2015-03-20 18:07:17 UTC  
> Url: https://github.com/boostorg/build/issues/65#issuecomment-84090658  

Figured out the source of the problem.  I should have imported the module. This is missing from documentation, though. I can provide a patch.

---

## Comment 2

> Username: vprus  
> Created at: 2015-03-20 18:18:42 UTC  
> Url: https://github.com/boostorg/build/issues/65#issuecomment-84092882  

Yep, patch or pull request for docs will be helpful.  
  
Thanks,  
  
On Пт, 20 марта 2015 at 21:07 Dmitry notifications@github.com wrote:  
  
> Figured out the source of the problem. I should have imported the module.  
> This is missing from documentation, though. I can provide a patch.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/build/issues/65#issuecomment-84090658.

---

## Comment 3

> Username: grisumbras  
> Created at: 2015-03-20 21:06:24 UTC  
> Url: https://github.com/boostorg/build/issues/65#issuecomment-84145852  

It turns out, using just plain `import my_module ;` does not work. It does allow invocation of rules from the module, but it also tries to load my_module.jam. I did not notice that at first, because I created my_module.jam while I tried to solve the problem. So, loading fails (with an error message) without the file, but everything else works, due to `IMPORT_MODULE` builtin rule used in `import` implementation.  In the end, I tried `IMPORT_MODULE my_module ;` and it works, but `IMPORT_MODULE` is not documented (as far as I see).  
  
So, I think `IMPORT_MODULE` should also be documented.

---

## Comment 4

> Username: swatanabe  
> Created at: 2015-03-21 16:10:33 UTC  
> Url: https://github.com/boostorg/build/issues/65#issuecomment-84380046  

AMDG  
  
On 03/20/2015 03:06 PM, Dmitry wrote:  
  
> It turns out, using just plain `import my_module ;` does not work. It does allow invocation of rules from the module, but it also tries to load my_module.jam. I did not notice that at first, because I created my_module.jam while I tried to solve the problem. So, loading fails (with an error message) without the file, but everything else works, due to `IMPORT_MODULE` builtin rule used in `import` implementation.  In the end, I tried `IMPORT_MODULE my_module ;` and it works, but `IMPORT_MODULE` is not documented (as far as I see).  
>   
> So, I think `IMPORT_MODULE` should also be documented.  
  
Agreed, but you should generally use import with  
a separate file instead of using "module" directly.  
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: grisumbras  
> Created at: 2015-03-24 15:40:24 UTC  
> Url: https://github.com/boostorg/build/issues/65#issuecomment-85566499  

I understand that. My point was that an example from the official manual does not work. I think that incomplete (and as it turns out even incorrect in some places) documentation is the largest problem of Boost.Build. Maybe maintainers could provide me with a guideline on what parts of documentation need contribution the most?  
P.S. PR for this particular issue is on way.
