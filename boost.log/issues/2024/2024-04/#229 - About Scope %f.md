# #229 - About Scope   %f [Closed]

> Username: kqbi  
> Created at: 2024-04-29 06:03:20 UTC  
> Updated at: 2024-04-29 11:53:42 UTC  
> Closed at: 2024-04-29 11:53:41 UTC  
> Url: https://github.com/boostorg/log/issues/229  

now  ，Use the following configuration：  
```  
boost::log::expressions::format_named_scope(  
             "Scope", boost::log::keywords::format = "%n (%f:%l)",  
             boost::log::keywords::iteration = boost::log::expressions::reverse,  
             boost::log::keywords::depth = 1);  
```  
You can get the following format：  
```  
[2024-04-29 13:57:22.057026|0x00007f45e51b4780][int main(int, char**) (/mnt/vdd/kqbi2/projects/gitlab/S_Log/test/main.cpp:25)][ERROR] 11111111111111111111111  
```  
How can I modify it to get the following format?  
```  
[2024-04-29 13:57:22.057026|0x00007f45e51b4780][int main(int, char**) (main.cpp:25)][ERROR] 11111111111111111111111  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2024-04-29 11:53:42 UTC  
> Url: https://github.com/boostorg/log/issues/229#issuecomment-2082526976  

You can use `%F` placeholder instead of `%f` to output file name without the path. See the [docs](https://www.boost.org/doc/libs/1_85_0/libs/log/doc/html/log/detailed/expressions.html#log.detailed.expressions.formatters.named_scope).
