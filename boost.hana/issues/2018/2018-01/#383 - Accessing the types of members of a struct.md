# #383 - Accessing the types of members of a struct [Open]

> Username: ldionne  
> Created at: 2018-01-22 00:23:06 UTC  
> Updated at: 2018-01-25 21:33:55 UTC  
> Url: https://github.com/boostorg/hana/issues/383  

I got this question by email, I'm posting it here for exposure (edited at will by myself):  
  
> I'm currently applying your library to the problem of generating the appropriate OpenGL infrastructure (creating vertex and fragment shaders, uniforms, per-vertex attributes, etc.) from a  minimal specification.   
> This is done using `BOOST_HANA_DEFINE_STRUCT` macro and the associated functionality. For example, following is a data type which represents a glyph rendered using signed-distance fields:  
>   
> ```c++  
> struct stroke_glyph {  
>     BOOST_HANA_DEFINE_STRUCT(stroke_glyph,  
>         (zui::rect,     rect),  
>         (zui::rect,     tex_coords),  
>         (zui::color,    color)  
>     );  
> }; // many more of these, each corresponds to an OpenGL shader.  
> ```  
>   
> What is the correct way to extract -- given only a type satisfying the `Struct` concept, rather than an instance -- the type (not the value) of a given field? There appear to be three ways of reflecting on a `Struct`, namely using accessors, keys, and methods. IIUC accessors is lower-level because you don't need to provide an instance, only a type. Since this corresponds to my scenario, I have opted to use this. I then go through the following gymnastics to access the type of the j-th member:  
>   
> ```c++  
> auto field = hana::at_c<j>(hana::accessors<OpType>());  
> auto type = hana::type_c<decltype(hana::second(field)(std::declval<OpType>()))>;  
> ```  
>   
> Is there another (better) way of doing this?  
  
You could also do this:  
  
```c++  
auto field_type = decltype(hana::at_c<j>(hana::members(std::declval<OpType>()))){};  
```  
  
But this suffers from limitations if you have reference or array types -- it will decay them, and it's still not as short and sweet as it could be. I could add this:  
  
```c++  
auto types = hana::member_types<SomeStruct>(); // a sequence of hana::types  
auto field_type = hana::at_c<j>(types);  
```  
  
But I'm worried this would require changing the MCD of `Struct` to do it properly, and that's a breaking change.

---

## Comment 1

> Username: seertaak  
> Created at: 2018-01-25 15:31:39 UTC  
> Url: https://github.com/boostorg/hana/issues/383#issuecomment-360501549  

Hi; I asked the question. What does MCD mean?  
  
In any case, such a construct would be nice. In general, I must admit that I am struggling to intuit the correct way of using the library in many cases. I pin this as my own fault; I had problems like this when starting to use range-v3, for example, and now I'm loving the library. So probably I need to slog through the hard part!

---

## Comment 2

> Username: ricejasonf  
> Created at: 2018-01-25 19:11:02 UTC  
> Url: https://github.com/boostorg/hana/issues/383#issuecomment-360568685  

MCD means "Minimal Complete Definition".  
  
To be a `hana::Struct` a type must have an implementation of `hana::accessors` which does not have an interface to let you get the raw type of the member.

---

## Comment 3

> Username: seertaak  
> Created at: 2018-01-25 21:33:55 UTC  
> Url: https://github.com/boostorg/hana/issues/383#issuecomment-360607720  

Ok, thanks for the information. I'm a beginner with this stuff, so I can't speak to the wisdom of expanding the MCD to allow this. Perhaps adding an example or a subheading to the docs would suffice, although personally I prefer the ergonomics of @ldionne's suggestion. Also: if everyone is using the macros to generate the underlying machinery anyway, then my naive thought is what's the problem (with breaking) :)
