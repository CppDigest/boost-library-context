# #51 - Missing documentation [Open]

> Username: albert-github  
> Created at: 2021-03-11 17:36:03 UTC  
> Updated at: 2023-09-20 11:56:42 UTC  
> Url: https://github.com/boostorg/sort/issues/51  

## Documentation  
  
In the file https://github.com/boostorg/sort/blob/develop/include/boost/sort/common/merge_block.hpp I see around the line 175  
```  
    //-------------------------------------------------------------------------  
    //  function :  
    /// @brief  
    /// @param  
    /// @return  
    //-------------------------------------------------------------------------  
    void merge_range_pos(it_index itx_first, it_index itx_mid,  
                         it_index itx_last);  
```  
  
- missing information for the end user about the usage of the function  
- this will give problems with documentation generators due to missing information / incorrect information  
  
  
For some other functions the documentation is also missing / incomplete whilst for a number of functions documentation is present.  
I didn't check other files.  
  
**Edit** just saw similar problems in https://github.com/boostorg/sort/blob/develop/include/boost/sort/flat_stable_sort/flat_stable_sort.hpp (e.g. line 107, 134).

---

## Comment 1

> Username: fjtapia  
> Created at: 2021-03-12 06:36:22 UTC  
> Url: https://github.com/boostorg/sort/issues/51#issuecomment-797271383  

Yes  
I saw yesterday. I will fill.  
Thanks  
  
El vie, 12 mar 2021 a las 1:27, spreadsort ***@***.***>)  
escribió:  
  
> Assigned #51 <https://github.com/boostorg/sort/issues/51> to @fjtapia  
> <https://github.com/fjtapia>.  
>  
> —  
> You are receiving this because you were assigned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/51#event-4448114424>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GB4ZMUE5JKCGWYLLDTTDFGULANCNFSM4ZAWWU3Q>  
> .  
>

---

## Comment 2

> Username: nigels-com  
> Created at: 2023-09-20 11:31:20 UTC  
> Url: https://github.com/boostorg/sort/issues/51#issuecomment-1727542554  

merge_block.hpp line 176  
  
```  
    //-------------------------------------------------------------------------  
    //  function :  
    /// @brief  
    /// @param  
    /// @return  
    //-------------------------------------------------------------------------  
    void merge_range_pos(it_index itx_first, it_index itx_mid,  
                         it_index itx_last);  
```  
  
merge_block.hpp line 216  
  
```  
//-------------------------------------------------------------------------  
//  function :  
/// @brief  
/// @param  
/// @return  
//-------------------------------------------------------------------------  
template<class Iter_t, class Compare, uint32_t Power2>  
void merge_block<Iter_t, Compare, Power2>  
::merge_range_pos(it_index itx_first, it_index itx_mid,it_index itx_last)  
{  
...  
```
