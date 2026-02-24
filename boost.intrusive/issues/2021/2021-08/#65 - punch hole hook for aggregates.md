# #65 - [Feature request] punch hole hook for aggregates [Closed]

> Username: denzor200  
> Created at: 2021-08-03 12:04:51 UTC  
> Updated at: 2021-11-18 11:20:52 UTC  
> Closed at: 2021-11-18 11:20:52 UTC  
> Url: https://github.com/boostorg/intrusive/issues/65  

Let's take a look at a synthetic example of an aggregate structure taken from my head:  
```  
struct Foo  
{  
    char ch;  
    int64_t value;  
    char ch1;  
    int64_t value1;  
    char ch2;  
    char ch3;  
    short sh;  
    short sh2;  
};  
```  
In my platform size of this structure is 40 bytes, where 24 bytes - real occupied by fields, but 16 bytes - its just a padding (proofs here: https://godbolt.org/z/cEbYe8ozo)  
  
The main idea behind this issue is why don't we start exploiting these padded bytes? For example, on the x64 architecture 16 bytes would be enough to place 2 pointers in them, respectively, we can declare an intrusive list without adding `list_member_hook<>` or `list_base_hook<>` to structure.  
  
I see it like that:  
```  
//This option will configure "list" to use the punch hole hook  
typedef punch_hole_hook<Foo> PunchHoleHookOption;  
  
//This list will use the punch hole hook  
typedef list<Foo, PunchHoleHookOption> FooList;  
  
//An object to be inserted in the list  
Foo foo_object;  
FooList list;  
  
list.push_back(object);  
  
assert(&list.front() == &foo_object);  
```  
  
FAQ:  
> 1. How we can implement this?  
  
In my opinion, for this task, it is enough to implement `punch_hole_hook<>` template class. If interested, I can prepare a drafted PR. This class can be implemented using the boost.pfr library(new reflection library from Antony Polukhin)  
  
> 2. Why is this needed?  
  
This is useful in some exotic cases to get the maximum memory optimization without reordering the structure fields.  
  
> 3. What are the disadvantages of this approach?  
       
- Non-portable code  
- Low speed of iterating over the elements of the list, due to the fact that the pointer to the next or prev element has to be unmarshalled from padded bytes  
- Low speed of add or delete operations by the list, due to the fact that the pointer to the next or prev element has to be marshalled into padded bytes  
- ??

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-11-18 11:20:52 UTC  
> Url: https://github.com/boostorg/intrusive/issues/65#issuecomment-972774691  

I don't think using padding is desirable, because it's UB. Boost.Intrusive offers totally customizable value<->node and node traversing traits. It's called `value_traits`:  
  
https://www.boost.org/doc/libs/1_77_0/doc/html/intrusive/value_traits.html  
  
with them you can implement yourself how to go from a node to another (maybe using data stored in padding bytes, I don't know).
