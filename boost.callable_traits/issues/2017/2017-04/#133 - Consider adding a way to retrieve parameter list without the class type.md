# #133 - Consider adding a way to retrieve parameter list without the class type [Open]

> Username: ldionne  
> Created at: 2017-04-17 02:55:01 UTC  
> Updated at: 2020-12-02 13:58:17 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/133  

Opened to track comment in https://lists.boost.org/Archives/boost/2017/04/234374.php:  
  
> I've a feeling that it might also be useful to provide an extractor that, for member pointers, returns just the arguments without the class.   
  
Right now, `args_t<void(foo::*)(float, char)>` is `std::tuple<foo&, float, char>`. It might be useful to provide a way of retrieving the argument list without the type of `*this`, such that one would get `std::tuple<float, char>`.

---

## Comment 1

> Username: PrakashChoure2002  
> Created at: 2020-12-02 13:58:17 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/133#issuecomment-737246269  

hii  
This is my first time contributing this project .  
can you guide me and should i take this project.
