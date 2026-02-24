# #25 - Reverting change for implicit_option that happened in boost 1.59 [Closed]

> Username: jmoraleda  
> Created at: 2016-12-09 00:49:47 UTC  
> Updated at: 2024-02-16 19:52:49 UTC  
> Closed at: 2017-06-10 19:46:09 UTC  
> Url: https://github.com/boostorg/program_options/issues/25  

Dear Vladimir Prus. This is a follow up to your comment in (http://stackoverflow.com/questions/31921200/how-to-have-an-optional-option-value-in-boost-program-options)  
  
implicit_option changed its behaviour in boost 1.59 so that --option_name option_value no longer works and needs to be written as --option_name=option_value. I would find it useful if the original behavior was restored and both formats worked (and the documentation changed to reflect this). Thank you.

---

## Comment 1

> Username: vprus  
> Created at: 2016-12-21 07:38:34 UTC  
> Url: https://github.com/boostorg/program_options/issues/25#issuecomment-268456721  

Thanks for your comment. I've posted on the mailing list to seek more opinions, and will restore the previous behavior unless I hear strong arguments against it.

---

## Comment 2

> Username: rimmartin  
> Created at: 2017-02-01 16:29:41 UTC  
> Url: https://github.com/boostorg/program_options/issues/25#issuecomment-276705567  

Anything to be done about this?  
  
My user simply won't use   
```  
--option_name=option_value  
```  
or  
```  
-ooption_value  
```  
or  
```  
--option_name="option_arg1 option_arg2"  
```  
And this also breaks from many common command line behaviors many people are familiar with including positional file input paths

---

## Comment 3

> Username: jdfr  
> Created at: 2017-02-22 13:37:05 UTC  
> Url: https://github.com/boostorg/program_options/issues/25#issuecomment-281670941  

This is also a pain point for me. I have many scripts scattered throughout several codebases that use the old "permissive" syntax, and they are broken in newer versions of Boost...

---

## Comment 4

> Username: rimmartin  
> Created at: 2017-03-10 16:56:06 UTC  
> Url: https://github.com/boostorg/program_options/issues/25#issuecomment-285722968  

Is there anyway to build boost dev while setting the program_options  
 revision back to 1.57?  Or would it be incompatible?  
  
Maybe fork and provide a way for users now stuck at 1.57 because of this module?

---

## Comment 5

> Username: jdfr  
> Created at: 2017-03-10 17:32:00 UTC  
> Url: https://github.com/boostorg/program_options/issues/25#issuecomment-285732503  

A workaround from  http://lists.boost.org/Archives/boost/2017/01/232272.php  
  
```c++  
template <typename T>   
struct greedy_implicit_value : public po::typed_value<T>   
{   
  using base = po::typed_value<T>;   
  greedy_implicit_value(const T& value) : base(nullptr)   
  {   
    base::implicit_value(value);   
  }   
  bool adjacent_tokens_only() const override { return false; }   
unsigned max_tokens() const override { return 1; }   
};   
template <typename T>   
po::typed_value* implicit_value(const T& value)   
{   
  return new greedy_implicit_value<T>(value);   
}   
```

---

## Comment 6

> Username: rimmartin  
> Created at: 2017-03-10 18:29:29 UTC  
> Url: https://github.com/boostorg/program_options/issues/25#issuecomment-285747208  

Maybe I don't know where to put that;  
  
I tried that back then but nothing happened  
  
Where does it go?

---

## Comment 7

> Username: rimmartin  
> Created at: 2017-03-10 18:30:54 UTC  
> Updated at: 2017-03-10 18:39:07 UTC  
> Url: https://github.com/boostorg/program_options/issues/25#issuecomment-285747556  

I did manage to build boost dev 1.64 for everything while setting the program_options to tags/boost-1.57.0  
```  
cd libs/program_options  
git fetch --all --tags --prune  
git checkout tags/boost-1.57.0  
cd ../..  
```  
Initial tests look good

---

## Comment 8

> Username: jdfr  
> Created at: 2017-03-11 11:39:11 UTC  
> Updated at: 2017-03-11 11:40:27 UTC  
> Url: https://github.com/boostorg/program_options/issues/25#issuecomment-285861432  

This is how I adapted it to my code; it works for me:  
  
  
```c++  
  
#if (BOOST_VERSION >= 105900)  
  template <typename T> struct greedy_implicit_value_impl : public po::typed_value<T> {  
    using base = po::typed_value<T>;  
    greedy_implicit_value_impl() : base(nullptr) {}  
    bool adjacent_tokens_only() const override { return false; }  
    unsigned max_tokens() const override { return 1; }  
  };  
  template <typename T> po::typed_value<T>* greedy_implicit_value() {  
    return new greedy_implicit_value_impl<T>();  
  }  
#else  
#  define greedy_implicit_value po::value  
#endif  
  
po::options_description makeOptions() {  
    po::options_description opts("options name");  
    opts.add_options()  
        ("option_name",  
            greedy_implicit_value<double>()->implicit_value(0.0)->value_name("opt"),  
            "option description")  
    return opts;  
}  
  
```

---

## Comment 9

> Username: vprus  
> Created at: 2017-06-10 19:46:09 UTC  
> Url: https://github.com/boostorg/program_options/issues/25#issuecomment-307586329  

I've pushed a series of commits to go back to original behavior, the last one being b35e654

---

## Comment 10

> Username: chambm  
> Created at: 2024-02-16 19:36:01 UTC  
> Updated at: 2024-02-16 19:52:49 UTC  
> Url: https://github.com/boostorg/program_options/issues/25#issuecomment-1949208554  

I am trying to convert a bool_switch option to an implicit_value one so I can change the default behavior so that the switch is on. If users want to turn it off in the future, I want them to use --option=false. This seems better than adding another parameter like "--no-option" and letting the "--option" become a vestigial no-op. But of course I don't want to break anybody's scripts that already use it like `--option some_file_path.txt`. Yet with the changes described here, the actual documented behavior of implicit_value, which is what I want, doesn't work when a positional argument follows the implicit_value one. Well, the behavior I want is documented on one of the `implicit_value` overloads and not the other actually:  
```  
/** Specifies an implicit value, which will be used  
            if the option is given, but without an adjacent value.  
            Using this implies that an explicit value is optional, but if  
            given, must be strictly adjacent to the option, i.e.: '-ovalue'  
            or '--option=value'.  Giving '-o' or '--option' will cause the  
            implicit value to be applied.  
            Unlike the above overload, the type 'T' need not provide  
            operator<< for ostream, but textual representation of default  
            value must be provided by the user.  
        */  
        typed_value* implicit_value(const T &v, const std::string& textual)  
        {  
```  
The other overload you removed the "but if given" part. Unfortunately it doesn't seem trivial to return to the behavior I need. Am I missing something, or how hard would it be to make it a `command_line_style::style_t` that would control whether implicit_options are allowed to consume other tokens?
