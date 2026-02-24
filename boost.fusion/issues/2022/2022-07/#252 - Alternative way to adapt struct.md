# #252 - Alternative way to adapt struct? [Open]

> Username: denzor200  
> Created at: 2022-07-05 10:30:22 UTC  
> Updated at: 2022-07-05 20:46:23 UTC  
> Url: https://github.com/boostorg/fusion/issues/252  

Why not make it possible to adapt structures in an alternative way (not only through `BOOST_FUSION_ADAPT_STRUCT`)? For example, like this:  
```  
namespace demo  
{  
    struct employee  
    {  
        std::string name;  
        int age;  
    };  
}  
  
// demo::employee is now a Fusion sequence  
namespace boost { namespace fusion {  
  namespace adapted {  
    constexpr auto tie_members(const demo::employee& x) noexcept  {  
      return std::tie(x.name, x.age);  
    }  
  }  
}}  
```  
  
or, like this:  
```  
namespace demo  
{  
    struct employee  
    {  
        std::string name;  
        int age;  
        constexpr auto tie_members() const noexcept  {  
          return std::tie(name, age);  
        }  
    };  
}  
```  
Does this fit into the Boost Fusion library? Does it make sense to create a PR? Even if no one uses this feature, it will somehow reduce the amount of code in https://github.com/boostorg/fusion/pull/243, and possibly speed up its review.

---

## Comment 1

> Username: daminetreg  
> Created at: 2022-07-05 11:49:32 UTC  
> Url: https://github.com/boostorg/fusion/issues/252#issuecomment-1174968656  

Hello, first apologies for the review of #243 is just me lagging hard because of my role in the startup I build, sorry for this, I really appreciate the great work integrating fusion with pfr, I just couldn't finish my review yet, I actually have started changing our code at tipi.build that uses Boost.Fusion to use the pfr enabled patch and so far it looks great.  
  
The only thing that bothers me is that we loose the string name representation of the field in comparison to non-PFR adaption that always populate it, do you have any alternative to do that ?  
  
Regarding the tie_members constexpr member that would be the same issue I would see, additionally without thinking too much why not consteval instead of constexpr, as this never has to run at runtime ?

---

## Comment 2

> Username: denzor200  
> Created at: 2022-07-05 20:43:04 UTC  
> Url: https://github.com/boostorg/fusion/issues/252#issuecomment-1175479667  

> The only thing that bothers me is that we loose the string name representation of the field in comparison to non-PFR adaption that always populate it, do you have any alternative to do that ?  
  
PFR does not provide such an opportunity, most likely it is not possible, not the fact that the language will allow this.  
If someone needs this in fusion, we will have to contrive.

---

## Comment 3

> Username: daminetreg  
> Created at: 2022-07-05 20:46:23 UTC  
> Url: https://github.com/boostorg/fusion/issues/252#issuecomment-1175482029  

Well I don't think it has been fusion goal in the design of fusion. I think I asked @djowel once about the fact of adding more reflection capabilities and he didn't thought it was a good idea to have it inside fusion.  
  
It just happens to be used heavily and relied upon. I wouldn't put it as something required, I was just curious if you had any trick or magic you used in such case. One could always map the member string name by instantiating templates manually, now that I think to it.
