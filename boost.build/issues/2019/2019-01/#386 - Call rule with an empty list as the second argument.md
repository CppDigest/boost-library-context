# #386 - Call rule with an empty list as the second argument [Closed]

> Username: Kojoley  
> Created at: 2019-01-12 14:56:05 UTC  
> Updated at: 2019-01-12 15:18:52 UTC  
> Closed at: 2019-01-12 15:18:52 UTC  
> Url: https://github.com/boostorg/build/issues/386  

MWE:  
```  
# define rule that accepts list as the first and second arguments  
rule llvm-config ( options * : components * )  
{  
    echo "option=$(options) component=$(components)" ;  
}  
  
llvm-config cxxflags ;  
llvm-config ldflags : ;  
llvm-config libs : engine ;  
```  
  
Output:  
```  
  
  
option=libs component=engine  
```  
  
Expected:  
```  
option=cxxflags  
option=ldflags  
option=libs component=engine  
```  
  
Am I doing something wrong or this is not supported? The documentation includes such a rule `rule create-file-from-another ( targets * : sources * : properties * )`.

---

## Comment 1

> Username: grisumbras  
> Created at: 2019-01-12 15:14:38 UTC  
> Url: https://github.com/boostorg/build/issues/386#issuecomment-453755174  

`components` list is empty, any expansion involving it results in an empty list. What you probably want to do is   
`echo "option=$(options)" "component=$(components)" ;`

---

## Comment 2

> Username: grisumbras  
> Created at: 2019-01-12 15:18:44 UTC  
> Url: https://github.com/boostorg/build/issues/386#issuecomment-453755506  

So, corrected rule still produces the same output?

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-01-12 15:18:52 UTC  
> Url: https://github.com/boostorg/build/issues/386#issuecomment-453755517  

Thanks, the problem was really with my Jam understanding.
