# #84 - Horizontal overflow with scrollbar in big mrdocs-generated member tables [Closed]

> Username: anarthal  
> Created at: 2025-01-31 23:36:46 UTC  
> Updated at: 2025-02-01 16:23:33 UTC  
> Closed at: 2025-02-01 16:23:31 UTC  
> Url: https://github.com/boostorg/boostlook/issues/84  

I'm seeing the behavior in this page: https://anarthal.github.io/mysql-mrdocs-v2/mysql/reference/boost/mysql/common_server_errc.html  
  
This is using the current master UI bundle.  
  
If there is any more info you need to investigate this, please let me know.

---

## Comment 1

> Username: rbbeeston  
> Created at: 2025-02-01 00:08:43 UTC  
> Url: https://github.com/boostorg/boostlook/issues/84#issuecomment-2628598286  

What behavior are you expecting? This seems to be behaving correctly.

---

## Comment 2

> Username: anarthal  
> Created at: 2025-02-01 00:21:35 UTC  
> Url: https://github.com/boostorg/boostlook/issues/84#issuecomment-2628610026  

Oh, maybe what I'm seeing is browser-specific. Under Firefox/Linux, I see this:  
  
![Image](https://github.com/user-attachments/assets/20825cc9-44a8-409a-b366-7afaf7caa776)

---

## Comment 3

> Username: anarthal  
> Created at: 2025-02-01 00:21:55 UTC  
> Url: https://github.com/boostorg/boostlook/issues/84#issuecomment-2628610322  

The second column overflows, and a horizontal scroll bar appears.

---

## Comment 4

> Username: alandefreitas  
> Created at: 2025-02-01 00:31:05 UTC  
> Url: https://github.com/boostorg/boostlook/issues/84#issuecomment-2628618460  

To be fair, converting adoc to html does generate a table with:  
  
```  
<colgroup>  
<col style="width: 50%;">  
<col style="width: 50%;">  
</colgroup>  
```  
  
Which can't hardly not overflow with that much text in the brief.

---

## Comment 5

> Username: rbbeeston  
> Created at: 2025-02-01 00:46:16 UTC  
> Url: https://github.com/boostorg/boostlook/issues/84#issuecomment-2628630277  

@alandefreitas that would be correct. if it's going outside the container, we would expect it to scroll.  
  
Although, if it's not being displayed inside of the boost.io site, it should be allowed different display rules.  
  
@julioest or @cdw9 do you have any suggestions?

---

## Comment 6

> Username: anarthal  
> Created at: 2025-02-01 16:23:31 UTC  
> Url: https://github.com/boostorg/boostlook/issues/84#issuecomment-2629015317  

I think I've found the issue: there is an extraordinarily long enumerator (`er_cant_set_gtid_next_list_to_non_null_when_gtid_mode_is_off`), and this causes the scroll. A similarly long word appears in the brief description column. Unfortunately, I have no control over the enumerator (it's provided by the MySQL server), so I can't do much about it.  
  
I don't see a viable solution in the Boostlook side. It could make sense to reduce the font size for this specific page, but I don't think this is achievable in Boostlook, and the gains are too small for the required effort. So I will be closing this.  
  
Thanks everyone for your comments.
