# #29 - BoostTestJamfile: Support multiple sources [Closed]

> Username: Flamefire  
> Created at: 2022-12-31 19:09:22 UTC  
> Updated at: 2023-01-02 18:30:14 UTC  
> Closed at: 2023-01-02 18:30:14 UTC  
> Url: https://github.com/boostorg/cmake/issues/29  

Currently the module ignores lines with multiple sources such as `run test_foo.cpp test_helpers.cpp ;`  
  
I'm not fully firm in B2 but I imagine we could change the regex to match anything but colon and semi-colon and maybe angle brackets so that this is supported. What do you think @pdimov ?

---

## Comment 1

> Username: pdimov  
> Created at: 2023-01-01 01:06:50 UTC  
> Url: https://github.com/boostorg/cmake/issues/29#issuecomment-1368312036  

That's not going to work in general, because the sources can also contain targets, such as e.g. `/boost//json`.

---

## Comment 2

> Username: Flamefire  
> Created at: 2023-01-01 13:33:25 UTC  
> Updated at: 2023-01-01 13:45:25 UTC  
> Url: https://github.com/boostorg/cmake/issues/29#issuecomment-1368445197  

Hm, then how about matching a list of cpp files: `^[ \t]*(run)[ \t]+([^ \t]+|([a-zA-Z_]+\.cpp[ \t]+)+)[ \t]*(\;[ \t]*)?$` (I wanted to keep the original "all non-whitespace stuff" for single, but strange filenames.  
  
BTW: In the original (current) RegEx `^[ \t]*(${types})[ \t]+([^ \t]+)[ \t]*(\;[ \t]*)?$` why is the semicolon at the end optional? Wouldn't that mistakenly find stuff like (I assume it's valid B2):  
  
```  
run /boost//json  
     test_src.cpp ;  
```  
  
So I'd think `^[ \t]*(${types})[ \t]+([^ \t]+)[ \t]*;[ \t]*$` would be better also removing the escape of the semicolon which I don't see why it would be required.  
  
Edit: Maybe `^[ \t]*(run)[ \t]+(([^ \t]+\.cpp[ \t]+)+);[ \t]*$` should be enough as "list of cpp files separated by space with a final semicolon", but that might include conditionals like `run foo.cpp <target-os>windows:bar.cpp ;`

---

## Comment 3

> Username: pdimov  
> Created at: 2023-01-01 14:51:47 UTC  
> Url: https://github.com/boostorg/cmake/issues/29#issuecomment-1368460391  

It's because I want to be able to do things like  
```  
run mytest.cpp  
  : : : something ;  
```  
and have it work (ignoring `something`.)

---

## Comment 4

> Username: Flamefire  
> Created at: 2023-01-01 16:34:07 UTC  
> Url: https://github.com/boostorg/cmake/issues/29#issuecomment-1368482238  

Good point. I was wondering if that really is useful as "something" could mean it isn't that easily added via `boost_test` but there is this in Boost.Locale:  
```  
run show_config.cpp : : : <test-info>always_show_run_output ;  
run test_message.cpp : $(BOOST_ROOT)/libs/locale/test ;  
```  
So I can use that there too.  
  
Back to the multiple sources case: I'll add a PR testing it locally for the use case I have. Then we can discuss the detail there.
