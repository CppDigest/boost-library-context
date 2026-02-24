# #57 - Improve instructions for regenerating core17_generated.hpp [Closed]

> Username: akrzemi1  
> Created at: 2020-10-19 19:45:22 UTC  
> Updated at: 2020-10-21 11:44:56 UTC  
> Closed at: 2020-10-21 11:41:03 UTC  
> Url: https://github.com/boostorg/pfr/issues/57  

The instructions on how to regenerate file `include/boost/pfr/detail/core17_generated.hpp` located at https://github.com/boostorg/pfr/blob/develop/misc/generate_cpp17.py#L70 say:  
  
```  
`python ./misc/generate_cpp17.py 300 > include/boost/pfr/detail/core17_generated.hpp`  
```  
The script is written in python2 and when I invoke this command on my default `python` -- which is version 3 -- I get syntax errors (`print` requires parentheses).   
  
I think you either need to rewrite the script to be python3, or change the instructions to invoke `python2`, or some combination of both of these.

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-10-21 11:43:02 UTC  
> Updated at: 2020-10-21 11:44:56 UTC  
> Url: https://github.com/boostorg/pfr/issues/57#issuecomment-713509225  

Thanks for the report!  
  
Now `generate_cpp17.py` works with Python2 and Python3 + added a smoke test that the script runs without issues
