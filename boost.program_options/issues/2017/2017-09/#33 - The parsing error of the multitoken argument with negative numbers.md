# #33 - The parsing error of the multitoken argument with negative numbers [Open]

> Username: espdev  
> Created at: 2017-09-20 22:03:41 UTC  
> Updated at: 2017-10-11 00:00:18 UTC  
> Url: https://github.com/boostorg/program_options/issues/33  

The parsing error of the multitoken argument with negative numbers occurs while setting negative numbers after the first number.  
  
For example, if we set an option  
```cpp  
("point,p", po::value<std::vector<float>>(&options.point)->multitoken(), "The point")  
```  
and enter in the command line the following values  
```  
prog -p -1 2 3  
```  
It works correctly.   
However, if we enter the values given bellow  
```  
prog -p -1 -2 3  
```  
the parsing error occurs  
```  
unrecognised option '-2'  
```  
  
This error exists in Boost 1.65.1 as well.

---

## Comment 1

> Username: zamazan4ik  
> Created at: 2017-09-20 22:06:00 UTC  
> Url: https://github.com/boostorg/program_options/issues/33#issuecomment-330994467  

I have the same issue. Anyone can fix it?

---

## Comment 2

> Username: espdev  
> Created at: 2017-10-08 18:16:19 UTC  
> Url: https://github.com/boostorg/program_options/issues/33#issuecomment-335027003  

This problem has existed for more than 7 years  
https://svn.boost.org/trac10/ticket/5201

---

## Comment 3

> Username: zamazan4ik  
> Created at: 2017-10-09 20:24:50 UTC  
> Url: https://github.com/boostorg/program_options/issues/33#issuecomment-335275472  

@vprus Can you give any feedback please?

---

## Comment 4

> Username: vprus  
> Created at: 2017-10-10 20:45:16 UTC  
> Url: https://github.com/boostorg/program_options/issues/33#issuecomment-335602339  

I think the current behaviour is not perfect, but I'm not sure what the proposed fix is? One can change the parsing level so that '-' followed by digit is not considered to be an option, but it can potentially break existing code that uses digits as short options.

---

## Comment 5

> Username: edgare  
> Created at: 2017-10-10 21:44:00 UTC  
> Url: https://github.com/boostorg/program_options/issues/33#issuecomment-335617406  

I patched boost to allow a fixed number of tokens, and allow those tokens to be anything. But breaks a bunch of cases and error messages. ex (IIRC):  
  
    ("point,p", po::value<std::vector<float>>(&options.point)->multitoken(2), "A 2D point")  
  
Then `--point 1 -f foo` would report `foo` as an unrecognized option, instead of `--point` having a missing parameter.  
  
I could rebase the patch if there's interest. But wouldn't recommend merging it.

---

## Comment 6

> Username: espdev  
> Created at: 2017-10-10 23:55:37 UTC  
> Updated at: 2017-10-11 00:00:18 UTC  
> Url: https://github.com/boostorg/program_options/issues/33#issuecomment-335641458  

@edgare I think this behavior can be fixed. For example, the standard argument parser from Python (argparse) works correctly in this case.  
```python  
import argparse  
p = argparse.ArgumentParser()  
p.add_argument('-p', '--point', nargs=2, type=float)  
p.add_argument('-f', '--foo')  
  
p.parse_args(['-p', '1', '-2', '-f', 'foo'])  
Out[11]: Namespace(foo='foo', point=[1.0, -2.0])  
  
p.parse_args(['-p', '1', '-f', 'foo'])  
error: argument -p/--point: expected 2 arguments  
```  
... but it breaks if exist any digits as short options
