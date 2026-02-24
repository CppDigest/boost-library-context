# #300 - BOOST_ATTRIBUTE_NODISCARD triggers -Wpedantic on clang-6 [Closed]

> Username: HDembinski  
> Created at: 2019-10-19 11:09:47 UTC  
> Updated at: 2019-10-21 11:53:05 UTC  
> Closed at: 2019-10-20 11:57:08 UTC  
> Url: https://github.com/boostorg/config/issues/300  

See issue on [Godbolt](https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(j:1,lang:c%2B%2B,source:'%23include+%3Cboost/config.hpp%3E%0A%0Astruct+BOOST_ATTRIBUTE_NODISCARD+Foo+%7B%7D%3B%0A%0Aint+main()+%7B%0A%7D%0A'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((g:!((h:compiler,i:(compiler:clang600,filters:(b:'0',binary:'1',commentOnly:'0',demangle:'0',directives:'0',execute:'1',intel:'0',libraryCode:'1',trim:'1'),lang:c%2B%2B,libs:!((name:boost,ver:'171')),options:'-O3+-DNDEBUG+-Wall+-Wpedantic+-Werror',source:1),l:'5',n:'0',o:'x86-64+clang+6.0.0+(Editor+%231,+Compiler+%231)+C%2B%2B',t:'0')),k:50,l:'4',m:71.99297629499561,n:'0',o:'',s:0,t:'0'),(g:!((h:output,i:(compiler:1,editor:1,wrap:'1'),l:'5',n:'0',o:'%231+with+x86-64+clang+6.0.0',t:'0')),header:(),l:'4',m:28.007023705004386,n:'0',o:'',s:0,t:'0')),k:50,l:'3',n:'0',o:'',t:'0')),l:'2',n:'0',o:'',t:'0')),version:4)  
  
I am not quite sure what to do about this, there are several options. I think this is not our fault, clang is inconsistent here. My expectation is that when I compile with `-std=c++14`, a compiler should simply not accept the attribute. But clang-6 does accept it anyway which is why the configuration tests pick this up. However, when pedantic warnings are enabled, it warns about the usage.  
  
So the general solution would be to run the configuration tests with `-Wpedantic -Werror`.

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-10-19 11:58:53 UTC  
> Url: https://github.com/boostorg/config/issues/300#issuecomment-544136369  

After looking into the code, I noticed that there is no configuration test for [[nodiscard]]. So this can be fixed in a simpler way. I will make a PR.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-10-20 11:57:08 UTC  
> Url: https://github.com/boostorg/config/issues/300#issuecomment-544245620  

Fixed in develop

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-10-21 11:53:05 UTC  
> Url: https://github.com/boostorg/config/issues/300#issuecomment-544479789  

Cool, thanks!
