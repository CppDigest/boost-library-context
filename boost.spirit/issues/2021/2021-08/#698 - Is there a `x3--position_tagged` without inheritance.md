# #698 - Is there a `x3::position_tagged` without inheritance? [Closed]

> Username: denzor200  
> Created at: 2021-08-30 21:22:56 UTC  
> Updated at: 2025-05-09 23:54:38 UTC  
> Closed at: 2025-05-09 23:54:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/698  

Hi everyone! Is it possible to add the `x3::position_tagged` attribute to a custom structure without applying inheritance? If it's not possible, it question should resolve, because in the future BOOST PFR library maybe will be used on the Spirit side (The corresponding PR has already been created in the neighboring Fusion repository)  
  
Recall that the pfr library is not able to work with inherited structures, the presence of inheritance for this library is not acceptable.  
  
If this issue has not been resolved yet, I suggest solving it using composition instead of inheritance:  
```  
struct employee  
{  
	int age;  
	person who;  
	double salary;  
        x3::position_tagged __position_tagged;  
};  
  
/// The library is aware of all the fields as they are listed in the BOOST_FUSION_ADAPT_STRUCT macro.  
/// Among these fields, a search is performed for the field whose type corresponds to x3::position_tagged, the name of the field is not important.  
/// Now this construction looks strange, but in the future the fields will be automatically detected using the PFR. The BOOST_FUSION_ADAPT_STRUCT macro will not be required  
BOOST_FUSION_ADAPT_STRUCT(client::ast::employee,  
    age, who, salary, __position_tagged  
)  
```

---

## Comment 1

> Username: saki7  
> Created at: 2025-05-09 23:54:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/698#issuecomment-2868112633  

Closing as per https://github.com/boostorg/spirit/pull/742#issuecomment-2865859762
