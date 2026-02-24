# #409 - Allow default values in hana define struct [Open]

> Username: quicknir  
> Created at: 2018-06-05 18:21:57 UTC  
> Updated at: 2025-04-10 17:56:45 UTC  
> Url: https://github.com/boostorg/hana/issues/409  

Current usage looks like this:  
  
```  
struct Person {  
    BOOST_HANA_DEFINE_STRUCT(Person,  
        (std::string, name),  
        (int, age)  
    );  
};  
```  
It's not clear from this whether the members will be declared as `int age;` or `int age{};`. Either way, one can't easily control the behavior. You can write a default constructor for Person by hand, but this involves repeating all the members and still has some limitations where multiple constructors are involved compared to inline initializers. Inline initializers are a fantastic feature and it would be fantastic to be able to simply write:  
  
```  
struct Person {  
    BOOST_HANA_DEFINE_STRUCT(Person,  
        (std::string, name, "Bob"),  
        (int, age, 42)  
    );  
};  
```  
  
Or something similar (you could for example, make the user write anything they want to be appear between the variable name and the semi colon, so it would instead be `(int, age, =42)`).  
  
This would be very useful for my purposes; I use Hana with POD structs but sometimes it would be nice to control their default construction without lots of boilerplate.

---

## Comment 1

> Username: danielchen0  
> Created at: 2018-07-11 19:37:22 UTC  
> Url: https://github.com/boostorg/hana/issues/409#issuecomment-404286050  

I think your number 2 example (int, age, =42) would be very useful for supporting generalized annotations, such as bitfields where we would write (int, age, :7). There are probably other sorts of annotations out there that could benefit from this.

---

## Comment 2

> Username: ricejasonf  
> Created at: 2018-07-11 20:45:35 UTC  
> Url: https://github.com/boostorg/hana/issues/409#issuecomment-404303691  

If you want more control you can simply use [BOOST_HANA_ADAPT_STRUCT](http://boostorg.github.io/hana/group__group-Struct.html#gaba3b4d2cf342bfca773e90fc20bfae91).  
  
It's slightly more redundant, but another custom macro could easily be created to use it for the above desired interface. There are probably a lot of use cases that could be added to the library, but I suspect it would bloat the interface and add compile time overhead.

---

## Comment 3

> Username: quicknir  
> Created at: 2018-07-11 23:01:22 UTC  
> Url: https://github.com/boostorg/hana/issues/409#issuecomment-404336893  

@ricejasonf not repeating member variables is one of the main reasons we want reflection in the first place. IMHO, the real use case of adapt struct is for classes/structs you don't control. Define struct is written well to allow you to add methods, aliases, CRTP, etc, anything you want to your class. There's only two things you can't do when you use the define struct, one is have different access levels for different members (which probably isn't sensible), and the other is what's described in the original issue. So, for define struct I don't think there are a bunch of different use cases/features at all, it's really primarily this one and it's really not going to be any dramatic increase in compilation time (it's just a matter of having the macro expand one additional thing).

---

## Comment 4

> Username: danielchen0  
> Created at: 2018-07-16 19:13:53 UTC  
> Updated at: 2018-07-16 19:16:57 UTC  
> Url: https://github.com/boostorg/hana/issues/409#issuecomment-405350336  

I should mention that something like   
  
    struct myStruct{  
     int myArray[40];   
    }  
  
is not too unreasonable either...  
  
So the proposed macro would be like  
  
    struct myStruct{  
      BOOST_HANA_DEFINE_STRUCT(myStruct,  
        (int, myArray, [40])  
      );  
    }  
  
in this case, as per @quicknir 's second version, where the annotation goes in the third argument. @ricejasonf I think if you consider implementing it this way, we are really only adding one new thing to the BOOST_HANA_DEFINE_STRUCT macro, not arbitrarily many new features.

---

## Comment 5

> Username: ricejasonf  
> Created at: 2018-07-16 19:39:17 UTC  
> Url: https://github.com/boostorg/hana/issues/409#issuecomment-405357476  

It wouldn't be my call to include it in the library. Are you adding a new macro or enhancing the existing one? If it breaks the existing macro it would not likely be accepted.  
  
For the case of the array, why not use `std::array<int, 40>`?  
  
It would be interesting to see it work with bit fields, but that might require reworking `Struct` from the ground up.

---

## Comment 6

> Username: mjhurd  
> Created at: 2018-07-17 09:51:17 UTC  
> Url: https://github.com/boostorg/hana/issues/409#issuecomment-405526086  

Here is an example file that supports initialisation such as:  
  
```  
struct workspace_t {  
  
  TUN_DEFINE_STRUCT(workspace_t, (std::string, "workspace", name),  
                    (std::string, "", notes), (std::vector<std::pair<win_info_t, int>>, , window_list),  
                    (int, 0, counter));  
};  
```  
  
Note, you have to specify the initializer, but you may leave it blank. The macro semantics are such that the last two items define the name and the initialiser. The other items specify the type which means that additional commas are OK as per the above example with the more complicated `std::vector<std::pair<`  type which includes an embedded comma as per the original `HANA` macro style.  
  
It is just a copy / paste / regex replace from the original `BOOST_HANA_DEFINE_STRUCT`.  
  
[struct_macro_helper.zip](https://github.com/boostorg/hana/files/2201094/struct_macro_helper.zip)

---

## Comment 7

> Username: quicknir  
> Created at: 2018-07-17 10:03:16 UTC  
> Updated at: 2018-07-17 10:09:02 UTC  
> Url: https://github.com/boostorg/hana/issues/409#issuecomment-405529460  

@mjhurd This approach is a bit problematic because initializers themselves can also easily have unparenthesized commas:  
  
```  
struct foo {  
  int x = f<3,4>();  
  vector<int> y{1,2,3};  
};  
```  
  
So maybe the right approach here is to have a new macro with different syntax:  
  
```  
struct Person {  
BOOST_HANA_DEFINE_INIT_STRUCT(Person,  
   (vector<int>)(y)({1,2,3}),  
   (int)(x)(f<3,4>())  
  )  
};  
```  
I.e. use the boost PP sequence approach which is a little less nice but more robust, instead of the tuple approach.

---

## Comment 8

> Username: mjhurd  
> Created at: 2018-07-17 23:55:10 UTC  
> Url: https://github.com/boostorg/hana/issues/409#issuecomment-405764407  

It is problematic but not a terrible compromise as far as it goes. The adapt macros are there for the trickier cases too. Not sure if I dislike adjoining commas or lots of parentheses more :smile: Having the third element in the parentheses list being optional would be a winner. Can't wait for reflection in 2023/6.

---

## Comment 9

> Username: dcolascione  
> Created at: 2019-04-21 01:02:16 UTC  
> Url: https://github.com/boostorg/hana/issues/409#issuecomment-485209297  

I'm looking at using locally-defined structures to describe Python argument parsing defaults. Being able to specify default values would be helpful: these default values would become the default values of the "parameters", which we need for optional parameters.

---

## Comment 10

> Username: mjhurd  
> Created at: 2019-04-21 01:17:34 UTC  
> Url: https://github.com/boostorg/hana/issues/409#issuecomment-485211775  

I think @quicknir is correct that my approach with possible empty initialisers is a bit ugly. Having an optional third parameter for an initialiser that may cope with commas with or without parentheses would be better. Though I'm not a fan of the `()()()` approach of a Boost PP sequence. Bad LISP and FP memories ;-)  
  
Here is my somewhat automagically generated from Hana `TUN_DEFINE_STRUCT` macro you can use until Hana provides something more suitable.  
  
[struct_macro_helper.zip](https://github.com/boostorg/hana/files/3100762/struct_macro_helper.zip)

---

## Comment 11

> Username: nikonru  
> Created at: 2025-04-08 14:37:54 UTC  
> Url: https://github.com/boostorg/hana/issues/409#issuecomment-2786676211  

So, have you come up with something?

---

## Comment 12

> Username: quicknir  
> Created at: 2025-04-10 17:56:44 UTC  
> Url: https://github.com/boostorg/hana/issues/409#issuecomment-2794702841  

@nikonru fwiw, if your use case is just basic reflection over a struct, and you're on C++20 or later, I'd probably recommend using boost pfr instead, which can handle the basic use case without any macros at all on the user side.
