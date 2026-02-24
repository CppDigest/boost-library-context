# #4 - Getting Started links to Boost.Build and other build related items don't work [Open]

> Username: RochaStratovan  
> Created at: 2019-05-21 16:47:02 UTC  
> Updated at: 2020-05-19 19:04:18 UTC  
> Url: https://github.com/boostorg/more/issues/4  

There are various links to Boost.Build in the 1.70.0 _Getting Started on Windows_ document (https://www.boost.org/doc/libs/1_70_0/more/getting_started/windows.html).  
  
These links are broken.  
Section 5.1: Boost.Build documentation: https://www.boost.org/build/doc/html/bbv2/overview/invocation.html  
  
Section 5.2: Boost.Build: https://www.boost.org/doc/libs/1_70_0/tools/build/index.html  
  
Section 5.2.1: Boost.Build: https://www.boost.org/doc/libs/1_70_0/tools/build/index.html  
  
Section 5.2.2 in the Boost.Build documentation: https://www.boost.org/build/doc/html/bbv2/reference/tools.html  
  
Section 5.2.2: build b2: https://www.boost.org/doc/libs/1_70_0/doc/html/bbv2/installation.html  
  
Section 5.2.4: Boost.Build documentation: https://www.boost.org/doc/libs/1_70_0/doc/html/bbv2/installation.html  
  
Section 5.4: Paragraph 2: here: https://www.boost.org/build/doc/html/bbv2/overview/configuration.html  
  
Section 7: Boost.Build reference manual: https://www.boost.org/doc/libs/1_70_0/tools/build/index.html

---

## Comment 1

> Username: mjcaisse  
> Created at: 2019-06-26 16:14:46 UTC  
> Url: https://github.com/boostorg/more/issues/4#issuecomment-505944040  

Thank you for the report @RochaStratovan

---

## Comment 2

> Username: mloskot  
> Created at: 2020-01-20 18:28:30 UTC  
> Url: https://github.com/boostorg/more/issues/4#issuecomment-576387637  

Copying @JGoldstone's comment from https://github.com/boostorg/website/issues/451 which may be adding some places that are missing from the report above:  
  
> These are all on https://www.boost.org/doc/libs/1_71_0/more/getting_started/unix-variants.html. (The counterpart links on the comparable Windows page are also broken.)  
>   
> The nonexistent page https://www.boost.org/doc/libs/1_71_0/tools/build/index.html is referenced in sections 5.2 ("Boost.build"), 5.2.1 ("Boost.build documentation"), 5.2.3 ("Boost.Build"), and 7 ("Boost.Build reference manual"). They all indirect to /home/www/shared/archives/live/boost_1_71_0/tools/build/index.html, which doesn't exist.  
>   
> 5.2.2's "in the Boost.Build Documentation" tries to take you to https://boostorg.github.io/build/doc/html/bbv2/reference/tools.html, which doesn't exist. That same nonexistent page is linked by 5.2.2's next sentence, in the Note, as the "building b2" link, and by 5.2.4's "Boost.Build documentation".  
>   
> The "here" in the second sentence of 5.4 tries to take you to https://boostorg.github.io/build/doc/html/bbv2/overview/configuration.html, which doesn't exist.  
>   
> These have been broken for some time (certainly in 1.70, not sure about 1.69); I would have spoken up sooner but didn't find this page until now.  
>
