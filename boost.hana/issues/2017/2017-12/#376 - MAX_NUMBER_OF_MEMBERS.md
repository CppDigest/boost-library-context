# #376 - MAX_NUMBER_OF_MEMBERS [Closed]

> Username: mjhurd  
> Created at: 2017-12-29 04:47:32 UTC  
> Updated at: 2018-02-10 22:21:55 UTC  
> Closed at: 2018-02-10 22:21:55 UTC  
> Url: https://github.com/boostorg/hana/issues/376  

Hi,   
  
Boost Hana's wonderfulness is unavailable for some autogen structs from table work I'm working with.   
  
I have more than 62 columns - to be members -  of the table - to be struct.  
  
Unfortunately, the ERB in hana/detail doesn't like the idea of more than 62 members in a struct.   
  
`MAX_NUMBER_OF_MEMBERS = (ENV["MAX_NUMBER_OF_MEMBERS"] || 40).to_i  
    raise "MAX_NUMBER_OF_MEMBERS must be <= 62" if MAX_NUMBER_OF_MEMBERS > 62`  
  
Pretty sensible for handcrafting but a bit limiting for my autogen work. Any thoughts on how best to raise the bar to 79 or 99 members?  
  
Kind regards,  
  
--Matt.

---

## Comment 1

> Username: ricejasonf  
> Created at: 2017-12-29 18:45:51 UTC  
> Url: https://github.com/boostorg/hana/issues/376#issuecomment-354484845  

It looks like the ERB is just generating preprocessor macros which have a limit to the amount of arguments that a macro can receive.  
  
The docs for `Struct` show that you can implement `hana::accessors` for your type directly instead of using the macro. It's not very pretty, but if you are generating code heterogeneously (outside of c++) that probably doesn't matter.  
  
See the examples below "Minimal Complete Definition" here: [Struct](http://boostorg.github.io/hana/group__group-Struct.html)

---

## Comment 2

> Username: mjhurd  
> Created at: 2017-12-30 05:44:36 UTC  
> Url: https://github.com/boostorg/hana/issues/376#issuecomment-354529105  

Thanks for the link to Struct. That'll do the job I need.

---

## Comment 3

> Username: ldionne  
> Created at: 2018-01-01 17:57:42 UTC  
> Url: https://github.com/boostorg/hana/issues/376#issuecomment-354666414  

I was away over the holidays. Reopening since I think we need a better solution. The ERB could generate the macros it needs for up to the right number of elements. In other words, we could lift the restriction that `MAX_NUMBER_OF_MEMBERS <= 62`.

---

## Comment 4

> Username: ldionne  
> Created at: 2018-02-10 22:21:55 UTC  
> Url: https://github.com/boostorg/hana/issues/376#issuecomment-364700431  

This should now be addressed. You can now generate `struct_macros.hpp` without any restriction on the number of members. This will be in Boost 1.67.
