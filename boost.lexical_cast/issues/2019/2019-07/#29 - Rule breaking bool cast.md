# #29 - Rule breaking bool cast [Closed]

> Username: rezaebrh  
> Created at: 2019-07-06 08:21:38 UTC  
> Updated at: 2019-07-12 11:51:02 UTC  
> Closed at: 2019-07-12 11:50:49 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/29  

In case of forcing `bool `value to be something other than `0 `or `1 ` , `lexical_cast ` won't throw any error. But the result is out of `bool` domain. This is where error might happen in case of re converting the converted value back to `bool`. For more detail check [boost::lexical_cast wrong output  
](https://stackoverflow.com/questions/56527471/boostlexical-cast-wrong-output)  
  
In our project, we use a function to convert and convert back some bool value to `std::string` and vise versa. When we are casting the value back to `bool`, we face `bad_lexical_cast `error. since the project is huge, we can't find out the rule breaking part but we hope some changes in boost will be helping us.

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-07-12 11:50:49 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/29#issuecomment-510857849  

Compilers optimize assuming that bool has either 0 or 1 value. Any attempt to deal with broken bool is doomed https://godbolt.org/z/NqdmeB (optimized away on clang in all cases, on GCC in majority of cases).  
  
Use UB sanitizer to find the corrupted bools in your project. Nothing could be done in lexical_cast
