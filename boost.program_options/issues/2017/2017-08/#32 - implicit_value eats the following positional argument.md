# #32 - implicit_value eats the following positional argument [Closed]

> Username: heavywatal  
> Created at: 2017-08-29 10:50:18 UTC  
> Updated at: 2018-02-22 09:07:12 UTC  
> Closed at: 2018-02-22 09:07:12 UTC  
> Url: https://github.com/boostorg/program_options/issues/32  

`implicit_value` seems a bit too greedy in v1.65. I guess it is related to the discussion in #25. For example, suppose `a.out` has an option `-v,--verbose` with `implicit_value(true)` and a positional argument.  
  
```  
# positional argument is set properly  
./a.out infile  
  
# -v is invalid; positional argument is empty  
./a.out -v infile  
  
# -v is set to false; positional argument is empty  
./a.out -v 0  
```  
  
Possible work-around for users will be like these:  
  
```  
# give the positional argument first  
./a.out infile -v  
  
# give an *explicit* value to the implicit option  
./a.out -v1 infile  
```  
  
I think that even in the case of `-v infile` the evaluation of positional/required arguments should precede the implicit value consumption given that those options already have implicit values.

---

## Comment 1

> Username: vprus  
> Created at: 2017-08-29 11:54:03 UTC  
> Url: https://github.com/boostorg/program_options/issues/32#issuecomment-325640506  

Thanks for the report. Yes, this behaviour is related to #25 - now that implicit value can be found in next token, "-v inline" means value of "inline" to option -v. I am not sure there's a good solution - while it's possible to specially case implicit option followed by a sequence of tokens none of which looks like an option, and consider such sequence to be positional options, that may be a confusing behaviour in its own right.

---

## Comment 2

> Username: heavywatal  
> Created at: 2017-08-29 14:07:55 UTC  
> Url: https://github.com/boostorg/program_options/issues/32#issuecomment-325675431  

Thanks. I agree that this change made the behavior more consistent in a sense, and now have found a solution. The problem was that I just didn't know the proper way to write simple boolean options:  
  
```c++  
# bad; eating next token  
po::value<bool>()->default_value(false)->implicit_value(true)  
  
# good  
po::value<bool>()->default_value(false)->implicit_value(true)->zero_tokens()  
  
# better  
po::bool_switch()  
```  
  
Sorry for bothering you by my ignorance.
