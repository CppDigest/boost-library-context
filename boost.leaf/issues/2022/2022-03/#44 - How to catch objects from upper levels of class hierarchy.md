# #44 - How to catch objects from upper levels of class hierarchy? [Closed]

> Username: arcrc  
> Created at: 2022-03-25 10:16:36 UTC  
> Updated at: 2022-04-06 06:27:03 UTC  
> Closed at: 2022-04-06 06:27:02 UTC  
> Url: https://github.com/boostorg/leaf/issues/44  

I am implementing some dummy class hierarchy like follows (SpecialAnimalHolderParent is actually needed as the classes on the left hand side are templated):  
```  
                                         AnimalHolder         SpecialAnimalHolderParent   
                                                  /             /  
                                               SpecialAnimalHolder  
                                              /                                
                          VerySpecialAnimalHolder  
```  
  
I would like to catch all instances of VerySpecialAnimalHolder and SpecialAnimalHolder by utlizing the following handler:  
`    [&](const SpecialAnimalHolderParent& error) {  
        std::cout << "SpecialAnimalHolderParent error object\n";  
        return 0;  
      }`  
  
However, the handler is never entered when I create a new error of VerySpecialAnimalHolder type.  
  
This is the godbolt implementation: https://godbolt.org/z/14cvqvMEh  
  
Is there something special that needs to be implemented to enable such behavior?

---

## Comment 1

> Username: zajo  
> Created at: 2022-03-25 20:02:20 UTC  
> Url: https://github.com/boostorg/leaf/issues/44#issuecomment-1079392460  

The only way to make your code work the way you want is to make `SpecialAnimalHolderParent` derive from `std::exception`, and then throw exceptions instead of using `leaf::result`, like so: https://godbolt.org/z/dPhh89Wo5.  
  
It is not a good idea to organize error types in a hierarchy; it is unfortunate that C++ exception handling works this way. With LEAF there are better options, but it would help if you provide more context: instead of using abstract cat/dog/animal hierarchies, can you describe what kind of errors you're trying to communicate?

---

## Comment 2

> Username: arcrc  
> Created at: 2022-03-26 09:26:40 UTC  
> Updated at: 2022-03-26 09:34:42 UTC  
> Url: https://github.com/boostorg/leaf/issues/44#issuecomment-1079648294  

Thank you for the reply! I was trying to design the error class hierarchy which would enable associating a specific error type with the available error category. I created a simple [UML diagram](https://viewer.diagrams.net/?tags=%7B%7D&target=blank&highlight=0000ff&edit=_blank&layers=1&nav=1&title=ErrorDesign#R7V1bd5s4EP41Pmf3ITmIi7EfY8dJ281u0ybbtH3pUYxiswHkxXId59dXAgFGyOYSsDkuT7EGIYHmm5lvNLLT08buy7UPF%2FO%2FsYWcnqpYLz3tsqeqKjBN%2BodJNqFkMNRCwcy3rVAEEsGd%2FYq4UOHSlW2hZaojwdgh9iItnGLPQ1OSkkHfx%2Bt0tyfspGddwBnKCO6m0MlKH2yLzPlbqGYif4fs2TyaGfSH4RUXRp35myzn0MLrLZE26WljH2MSfnJfxshhixety8P7zYNz89y%2F%2FvBp%2BT%2F8d%2FTX%2FT9fzsLBrsrcEr%2BCjzxSeejX56erd1%2F0%2F74ubq8%2BrZXri8cPZ%2FwW5Sd0Vny9Ju6CbCZ04X3%2B1mQTLeVybbsO9Ghr9IQ9csev0IUYQceeefTzlD4h8qngJ%2FKJTbVwwS8QvKDS6dx2rBu4wSv2HksCp89RazTHvv1Kh4UOvQSogF72CQeU2k%2F1uGN3UrFCpT5a0j630eKAWHQDl4T3mWLHgYul%2FRg8MOviQn9meyNMCHajgfDKs5DFW7G2gwbx8XOMH3Z%2FQZVw1bHVQC9bgOQqukbYRcTf0C7RVYWrhJvbkDfXCXZBn8vmW7iNZJCbyyweOZ7sMzUv6M3oEsSzqVp6NqAXnI7qIzUddKjaPUjQiC3ichuI9MPWiyaiAJ4loKpnoJoBqGMH4JwTN8GQqLgIqQ56IhKcurZlseFGywWc2t7snuH28gwkkpvgxkstkXzm66IFICKQwBBnDDkOfETOLV7axMZsfD%2FsO1pg2yPB4hmjnnEZSHwyxh59YmgHmEIUv2vEMCxB215rzkfbJq3GPHUPdoMrpeayOu1ndEqQu3Aojnpq32FvTjYL5EGXYfYL63VP2%2FTaLFwVer8G2DIH10p5LeUAXstQfhOvdRajhgNL60scyVCCLKBWclxAcFzaoL2Oy8yA%2FKw3VnsXShrTYYcfGfxSFZB8vxUiNe2i9KyLYiJM731yApTMqbdDnsRtpd3TiD7qWDk3mKNSx7QNknZdvqtfGHNc5UZBhIkar815DbuAVLtSTUlAkim1qYAEgESpI8FQZ4gEzT%2F%2B7Iy1oF4H%2BpGNFaj51oo864JlnLT16GAWqkdUxGN5QDNo88pmExcJtunIjCyalvK5kPOI15NEMAoE9EIElNKheolX%2FhTla4ySE4refArJnnavXmVx1keUudk%2F04n3nrh%2By3C7lYwIqY%2BqaOkhwpfkd21numUHClchM1BdAR9oZWjtxFu5YwculzuobXy9o7fHord9lbrPFN00jIw%2Fo0IJvdUq0VtNL0BvzXbQW5BNzM%2BysFYQbf%2F4vSlu7BfewnGlMGsubBodya1frRKWK1VrDSx3MkFz9fv08f76%2FsfknaJ%2FWnx%2FPZOT3CAnTZttIKKhkklPm%2FJmlFdY8WU474GNN7u99ltx3sgwW016gRDpzYqkVxMGUk3hWZomvdltrn2kl3HZXYS347nH4rnCrqqEdcZ4TbFcoxLLNYz0dG2uPkXb1CLLTXCsnCCx3YNKTveUEjzJLIzHPfRXhj9dbyiCqtlMvqO%2Fb1brUEKMZGptapNXlZWSR4I1M9bLmifLeBtQLFDUYpqtg%2FJKM5usl97HeC0butiz7ue2lyG6CQtW9bR5Cxz3xSZf%2BRX2%2BRuTU0WErcuXrW6Xm3hs%2Bq5ftxvhXaoRtZP7glZ0o5Sn1siYlYKEOYTGYQjyUCAJ4o5aUYKsC3tq8f5ZDkGmYIGbrW7cEN%2FGMaTgLbDX0qp0LYJ%2BAvdv22iXQr9GBEvXsH8wYBriEQSRBBcFJhgIwBQHKg3MHRliFHejDJFvYicoDkesFdOlTvgULoU82GTeHfkpkSvmbG%2FtNrmduaKWgb%2BsJCJLFk%2BqJCIFfXYr5Pz8%2FERpZA6yyjjpfUWPQ5LIQX4cPvGkr36lSksezeR80qfLHtbqTLK49qSljEOaJMgmdmCX9jJGOcYOJQnapYdDSkDpsSDKZQWxsa7nNkF3VNFszrUPF5l43FTANQwzFQAl3wyQ7ZXui7ZvU4ma7ybbla7EWfd5nGd%2F4yPvS7rbmOYULm0dMFM3IwBEDE082l00ITLFUtawbZl6yRNee4pdn1feve12eczR8hix5hWjNnd3%2BpRqXnKUd4d%2FqlCpnGMhRYtajR3%2Bqa1Qf4Nn9rTzXMfyXINB57l2YbxL4St5rh1ksSWeS1q37TKOw2QcWvTjDVsZR6nw10TGoaWP5euiZyuacQzEzWXx6%2FRHzzjULBerGLXH2F1gj4LiboGm9lMXwY8XwUH87ecuhGcAX%2BDwchfCd%2FiJ9obwbPLR8hBeIRRXDftNh%2FAo82vTpiEAIB3D%2B32jYgwXnJv%2B5hi%2BYx5xd3KQ%2Bt2lZo5RqAUSms5uGrKbiHu1ym4UgfuaoKLdmGYazwZoxm6Aou41nOwNhvA97j44wIEltcDPerTL0k4pyZSUtUrtVDRgacNBTUmmqQsDvdnQ6ubcUSmkw%2F4xsC%2BJMqUofhNRRlVrAv9w2HrwZ78VXXGH5f3HbkulLeVcveh3GE9%2FR0VT8717t6Oywy%2B0dkdFq%2B0oyscV%2BfgUwq%2FzX20p6nb%2BK0F69wvAlfxXu4%2BjaPUXti4699WailbnvxKkdxWtSv7rWBUt2kz%2BuUMIguRfZGiTXw%3D%3D) to depict what I mean.   
  
Let's assume there are many components where all different kinds of errors can occur and the goal is to have a unified mechanism to handle all of them. One way to specify all the errors in the component would be to create an enum class and list all the possible errors. A disadvantage of that approach would be that there might be duplicated errors accross different components, e.g. run-time error, out-of-bounds error etc. To avoid it, I have come up with another idea. I decided to extract those more general error errors into classes such that they do not need to be specified in the component enum class.  
I admit that I might have been biased by the hierarchy provided by C++ exception and somehow thought it might be a good idea to follow it.  
Is there some better way to design such system or to improve the current design?

---

## Comment 3

> Username: zajo  
> Created at: 2022-03-26 21:59:48 UTC  
> Url: https://github.com/boostorg/leaf/issues/44#issuecomment-1079782640  

The LEAF way is to use error types as tags, this technique is described in the tutorial: https://boostorg.github.io/leaf/#tutorial-classification.  
  
Using your types, `RunTimeError<ErrorType>`, presumably `ErrorType` is the kind of `RunTimeError` that has been detected. With LEAF, instead of using `RunTimeError<DivisionByZero>`, we could say:  
  
```  
struct RunTimeError { };  
struct DivisionByZero { };  
```  
  
Then, simply pass both types to `leaf::new_error`:  
  
```  
leaf::result<T> f()  
{  
  if( error_detected )  
    return leaf::new_error( RunTimeError{}, DivisionByZero{} );  
}  
```  
  
To handle:  
  
```  
leaf::try_handle_all(  
    []() -> leaf::result<int>  
    {  
        return f();  
    },  
    []( RunTimeError, DivisionByZero )  
    {  
        // Handle RunTimeError / DivisionByZero  
    },  
    []( RunTimeError )  
    {  
        // Handle RunTimeError (we don't care what kind)  
    },  
    []  
    {  
        // Handle all other errors  
    } );  
```  
  
See it on godbolt: https://godbolt.org/z/q3Kdchxa7

---

## Comment 4

> Username: arcrc  
> Created at: 2022-03-27 10:56:41 UTC  
> Url: https://github.com/boostorg/leaf/issues/44#issuecomment-1079904947  

It seems that there is additional freedom to create the custom error hierarchies depending on the errors that are instantiated by `leaf::new_error` and by the matching parameters in the handlers.  
  
I think I have not documented my UML diagram well. `ErrorType` type must be either `EmptyError` or any of the derived classes. That is, I would like to have the possibility to instantiate `RunTimeError<ValueError<double>>` or `RunTimeError<EnumClassError<DummyEnum>>` in one of the components depending on the needs. Then in the handler, I would like to do the following  
```  
    [](const RunTimeError<EnumClassError<DummyEnum>>& err )  
    {  
        auto error_val = err.getError().getEnumValue();  
    }  
```  
If the error does not need to carry any additional information I am also able to create it and handle it   
```  
    [](const RunTimeError<>& err )  
    {  
        // type of err.e_ is EmptyError  
    }  
```  
What I could not do is to catch all possible RunTimeError types (due to template type) and the naive approach was to introduce some additional class like `RunTimeErrorParent` that could be used to catch `RunTimeError<ValueError<double>>`, `RunTimeError<EnumClassError<DummyEnum>>` or whatever template type is specified. That's however what did not work and what I wanted to ask about in my first post.  
  
From what I understood I should make all classes below `Error` derive directly from it (`IOError` should derive from `Error` and not from `RunTimeError`). Then I can make the hierarchy myself based on what errors are created and what matching parameters I specified in the handlers. If I want to catch all `RunTimeError` instances I might need to create an empty `RunTimeErrorParent` struct and use it like:  
```  
leaf::result<T> f()  
{  
  if( error_detected )  
      leaf::new_error( RunTimeErrorParent{}, RunTimeError<EnumClassError<DummyEnum>>{...} );  
}  
```  
And the handler:  
  
```  
[]( RunTimeErrorParent, RunTimeError<EnumClassError<DummyEnum>> )  
    {  
        // handle specific RunTimeError  
    },  
    []( RunTimeErrorParent )  
    {  
        // handle generel RunTimeError  
    }  
```

---

## Comment 5

> Username: zajo  
> Created at: 2022-03-27 21:45:08 UTC  
> Url: https://github.com/boostorg/leaf/issues/44#issuecomment-1080025181  

I don't recommend using a hierarchy, and I don't recommend using types void of specific semantics.  
  
Let's say you have a `class widget`, and you want to communicate that negative widget widget width or height is not allowed. You want to report this as an instance of `RunTimeError<ValueError<double>>`. Instead, I recommend the following:  
  
```  
struct widget_error { };  
struct invalid_widget_size_error { };  
struct requested_widget_width { int value; };  
struct requested_widget_height { int value; };  
```  
  
Then:  
  
```  
leaf::result<widget> make_widget( int width, int height )  
{  
  // Automatically associate error objects with any failure returned from this scope:  
  auto load = leaf::on_error( widget_error{}, requested_widget_width{width}, requested_widget_height{height} );  
  
  // Check for invalid width/height, report invalid_widget_size_error:  
  if( width < 0 || height < 0 )  
    return leaf::new_error( invalid_widget_size_error{} );  
  
  // Check for invalid other things, report other errors:  
  ....  
  
  // Return a valid widget:  
  return widget{ width, height };  
}  
```  
  
With this setup, any error in making a widget will automatically be tagged as `widget_error`, and will include the `width` and `height` of the widget that failed to initialize. The call to `leaf::new_error` communicates 4 error objects, of the following types: `widget_error`, `invalid_widget_size_error`, `requested_widget_width`, `requested_widget_height`.  
  
Now we can write all kinds of useful error handlers. For example, to handle any `widget_error`, we'd use:  
  
```  
[]( widget_error )  
{  
  std::cerr << "widget error detected" << std::endl;  
}  
```  
  
To handle any widget error, optionally reporting the widget width and height (presumably not all widget errors carry that info), we'd use:  
  
```  
[]( widget_error, requested_widget_width const * w, requested_widget_height const * h )  
{  
  std::cerr << "widget error detected" << std::endl;  
  
  if( w )  
    std::cerr << "widget width: " << w->value << std::endl;  
  
  if( h )  
    std::cerr << "widget height: " << h->value << std::endl;  
}  
```  
  
Or we can be more specific and handle `invalid_widget_size_error`, which we know also communicates `requested_widget_width` and `requested_widget_height`, so there is no need to take `w` and `h` as pointers:  
  
```  
[]( invalid_widget_size_error, requested_widget_width w, requested_widget_height h )  
{  
  std::cerr << "invalid widget size detected, width=" << w.value << ", height=" << h.value << std::endl;  
}  
```  
  
Don't use hierarchies for error handling, it is a bad idea from the last century. :)

---

## Comment 6

> Username: arcrc  
> Created at: 2022-03-28 12:23:50 UTC  
> Updated at: 2022-03-28 12:57:49 UTC  
> Url: https://github.com/boostorg/leaf/issues/44#issuecomment-1080585090  

Note: I left my thought process below for the reference. You might want to start reading from the **Edit** section.  
  
How would this solution scale? Imagine there are 10 different unrelated components e.g. `Widget`, `Car`, `Cat`, `House`, etc. Each of those components would like to report `OutOfBoundsError` together with some different value type.  
Would you store it as so?  
```  
// In widget  
struct out_of_bounds_widget_error{};  
struct requested_widget_bound{ int index; };  
  
// In car  
struct out_of_bounds_car_error{};  
struct requested_car_bound{ double some_other_value; };  
...  
```  
The another solution would be to discourage such general errors and force each component to define more specific error, but I am not sure if this is a good idea, so let's stick to the initial idea.  
The disadvantage is that If there is more such general errors then there would be a lot of duplicated errors stored in many separate structs.  
I would say this might not be a big issue as long as the error is handled directly in the given component.  
Now, imagine there is a sequence of calls that utilizes all of those objects (lets say in component 11) and which is guarded by `try_handle_all`. To handle all OutOfBoundsErrors I would need to implement a handler that would take `requested_widget_bound` or `requested_car_bound` and so on. I would need to go into the component documentation to find out the exact types. I think this might cause some overhead which could be avoided.  
  
Aren't you also using the hierarchy, not by using the inheritance but by defining the tags? I mean, this hierarchy is built using tools like `leaf::on_error`, `leaf::new_error` and the handlers. Of course this type of hierarchy is much more powerful because it can be freely customizable. In your example  
```  
[]( widget_error )  
{  
  std::cerr << "widget error detected" << std::endl;  
}  
```  
this handler would handle all the errors that are created by `leaf::new_error(..)` in `leaf::result<widget> make_widget( int width, int height )` if there is not a more specific handler. In this context `widget_error` might be considered as a parent error class.  
  
Would my design make sense if I say that I use the inheritance to have the common interface, but not to build the class hierarchy? To build the hierarchy (which can be customized across all components) I can use the tools that leaf provides.   
Am I missing something or possibly misusing some leaf features?  
  
Lastly, the example you provided but based on my design.  
```  
Turns out that it is not easily convertable.  
```  
**Edit:**  
I think it finally clicked what you wanted to share. My design turned out to be bulky, because it was not possible to pass two values simultaneously.  
Let me answer my question regarding OutOfBoundsErrors to see if I got it right.  
```  
//in some header which all components include  
struct out_of_bounds_error {}; // for tagging  
  
//in widget  
struct widget_error { };  
struct requested_widget_bound{ int index; };  
  
//in car  
struct car_error { };  
struct requested_car_bound{ double some_other_value; };  
```  
  
Creating error in widget:  
```  
leaf::result<widget> make_widget( int width, int height )  
{  
  // Automatically associate error objects with any failure returned from this scope:  
  auto load = leaf::on_error( widget_error{}, requested_widget_width{width}, requested_widget_height{height} );  
  
  // Check for invalid width/height, report invalid_widget_size_error:  
  if( width < 0 || height < 0 )  
    return leaf::new_error( invalid_widget_size_error{} );  
  
  //dummy condition  
  if( width==20)  
    return leaf::new_error( out_of_bounds_error{},  requested_widget_bound(20));  
  
  // Return a valid widget:  
  return widget{ width, height };  
}  
```  
Handler:  
```  
[]( out_of_bounds_error, requested_widget_bound error_bounds_val )  
{  
  std::cerr << "invalid bound detected =" << error_bounds_val.value << std::endl;  
}  
  
```  
Would this be the preferred way to handle such errors? Then I guess I do not need template types anymore.

---

## Comment 7

> Username: zajo  
> Created at: 2022-03-29 21:14:00 UTC  
> Updated at: 2022-03-29 23:04:08 UTC  
> Url: https://github.com/boostorg/leaf/issues/44#issuecomment-1082380080  

Yes, though you can break it down even further if that is practical:  
  
```  
struct out_of_bounds_error {};  
struct requested_index { int value };  
struct widget_error {};  
struct car_error {};  
```  
  
However, there's no reason why `out_of_bounds_error` and `requested_index` should be separate types: you will always know the index when reporting out of bounds error. So, it's better to rename it to `index_out_of_bounds_error` and store the index in it, so now we have:  
  
```  
struct index_out_of_bounds_error { int value; };  
struct widget_error {};  
struct car_error {};  
```  
  
The reason to use `value` instead of `index` as the identifier for that member is that by default LEAF is automatically able to print it if you use `leaf::verbose_diagnostic_info`. Alternatively, you can name it `index` and provide an `operator<<` overload for `out_of_bounds_error` for use in diagnostics.  
  
Perhaps it makes sense to add more members:  
  
```  
struct out_of_bound_error { int index, min_bound, max_bound; };  
```  
  
Or you could do:  
  
```  
struct index_out_of_bound_error { int value; };  
struct min_bound { int value; };  
struct max_bound { int value; };  
```  
  
Breaking things up like this makes sense if you don't always know the `min_bound` and the `max_bound` when reporting out of bound errors; in this case you can write handlers for either case.  
  
The ability to efficiently communicate many objects of different types for any given failure makes classification much more practical than what could be done by throwing a single exception object which then has to fit a type hierarchy. It also lets us provide context. Consider:  
  
```  
leaf::result<int> add_values( std::function<leaf::result<int>()> f, std::function<leaf::result<int>()> g )  
{  
  auto load = leaf::on_error( add_values_failure {} );  
  BOOST_LEAF_AUTO(a, f());  
  BOOST_LEAF_AUTO(b, g());  
  return a + b;  
}  
```  
  
No matter what failure is detected in the call to `f` or the call to `g`, that failure will automatically acquire an additional `add_value_failure` label; and now we can write an error handler just for that case, if needed -- yet if nobody cares to handle `add_value_failure` specifically, LEAF won't bother communicating that object.

---

## Comment 8

> Username: zajo  
> Created at: 2022-04-06 06:27:02 UTC  
> Url: https://github.com/boostorg/leaf/issues/44#issuecomment-1089870447  

I'm assuming this is resolved, feel free to reopen if you have more questions.
