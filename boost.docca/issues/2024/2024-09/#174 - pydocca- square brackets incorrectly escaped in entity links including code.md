# #174 - pydocca: square brackets incorrectly escaped in entity links including code [Closed]

> Username: anarthal  
> Created at: 2024-09-02 17:04:14 UTC  
> Updated at: 2024-10-03 13:14:40 UTC  
> Closed at: 2024-10-03 13:14:39 UTC  
> Url: https://github.com/boostorg/docca/issues/174  

When having code like:  
  
```cpp  
/// Check \ref row::operator[] before proceeding  
struct row {  
    /// Some operator  
    int operator[](int);  
};  
```  
  
The reference to `operator[]` renders extra backslashes. The generated quickbook looks like:  
  
```  
[link mysql.ref.boost__mysql__row.operator__lb__rb_ `row::operator\[\]`]  
```  
  
Those backslashes shouldn't be there. I think the cause is in [this line](https://github.com/boostorg/docca/blob/5f349f0c7b81efa6f26cd1014cc15f5addfbbc87/include/docca/quickbook/components.jinja2#L686), which is not propagating the `in_code` parameter.
