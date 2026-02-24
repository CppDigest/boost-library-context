# #93 - b2 jamfile syntax warning [Closed]

> Username: jeking3  
> Created at: 2018-10-27 13:25:32 UTC  
> Updated at: 2018-10-27 17:15:17 UTC  
> Closed at: 2018-10-27 16:40:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/93  

```  
../libs/multiprecision/test/Jamfile.v2:58: Unescaped special character in argument <toolset>msvc:<cxxflags>/fp:precise  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2018-10-27 15:59:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/93#issuecomment-433632583  

Do you know which character is regarded as special in this? I get this warning all the time and have no clue how to fix it.

---

## Comment 2

> Username: pabristow  
> Created at: 2018-10-27 16:26:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/93#issuecomment-433634676  

colon is a special char (along with space!) in the daft jam language.  
  
" A backslash (\) can escape a double quote or any single whitespace character. "  
so you could try that?

---

## Comment 3

> Username: NAThompson  
> Created at: 2018-10-27 16:40:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/93#issuecomment-433635829  

A colon sets a low bar for a special character. In any case, it's fixed in  [30bc417](https://github.com/boostorg/multiprecision/commit/30bc41706d8e8a1cb7e7f060c075123acb4a164c).

---

## Comment 4

> Username: jeking3  
> Created at: 2018-10-27 17:15:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/93#issuecomment-433638567  

Thanks for tackling that so quickly.
