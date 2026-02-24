# #122 - Deleted member function specifier not being rendered [Closed]

> Username: alandefreitas  
> Created at: 2022-03-22 17:40:29 UTC  
> Updated at: 2022-04-15 15:43:33 UTC  
> Closed at: 2022-04-15 15:43:33 UTC  
> Url: https://github.com/boostorg/docca/issues/122  

A [deleted member function](https://github.com/CPPAlliance/url/blob/3f9954e5596d7cf75c23e3a95eb00f6864c660ae/include/boost/url/params.hpp#L246) in boost.url:  
  
```  
template<class FwdIt>  
    void  
    assign(FwdIt first, FwdIt last,  
        std::input_iterator_tag) = delete;  
```  
  
is being rendered without `= delete`. Without the `= delete;` it just looks like a regular function that has not been properly documented.  
  
![image](https://user-images.githubusercontent.com/5369819/159541506-6d140f7d-8d8a-4e9d-8a72-63dcaf9fb7a1.png)  
  
![image](https://user-images.githubusercontent.com/5369819/159541575-385f8260-c217-4f09-ad30-2b3fa55ff8d0.png)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-03-22 21:10:45 UTC  
> Url: https://github.com/boostorg/docca/issues/122#issuecomment-1075642994  

Is the data in the XML file that doxygen produces ?

---

## Comment 2

> Username: evanlenz  
> Created at: 2022-03-22 21:20:03 UTC  
> Url: https://github.com/boostorg/docca/issues/122#issuecomment-1075652424  

That's what I've been looking for. Initial indicators say NO (though it is in the Doxygen-generated HTML). But there's another context where "=delete" _is_ showing up in the XML (for a different function):  
  
https://github.com/CPPAlliance/url/blob/3f9954e5596d7cf75c23e3a95eb00f6864c660ae/include/boost/url/segments.hpp#L498  
  
That example produces XML that includes the following:  
  
```xml  
<argsstring>(iterator before, FwdIt first, FwdIt last, std::input_iterator_tag)=delete</argsstring>  
```  
  
whereas the corresponding snippet in the XML for the example in this issue is as follows:  
  
```xml  
<argsstring>(FwdIt first, FwdIt last, std::forward_iterator_tag)</argsstring>  
```  
  
I don't yet know why `=delete` appears in one place but not the other.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-03-22 23:27:56 UTC  
> Url: https://github.com/boostorg/docca/issues/122#issuecomment-1075747909  

maybe a newer Doxygen behaves correctly?

---

## Comment 4

> Username: evanlenz  
> Created at: 2022-03-25 04:45:55 UTC  
> Url: https://github.com/boostorg/docca/issues/122#issuecomment-1078651057  

Unfortunately, the only difference I see in the resulting XML (for this function) between Doxygen 1.8.15 and Doxygen 1.9.3 is the addition of the following (unrelated) line:  
  
```xml  
<qualifiedname>boost::urls::params::assign</qualifiedname>  
```  
  
But there is still no mention of `=delete` or any other new indicator for this function.  
  
The next test, I think, would be to see if DoxyPress fixes this.

---

## Comment 5

> Username: evanlenz  
> Created at: 2022-03-25 05:26:10 UTC  
> Updated at: 2022-03-25 05:27:23 UTC  
> Url: https://github.com/boostorg/docca/issues/122#issuecomment-1078668196  

Wait a sec... it seems I've been improperly impugning Doxygen for this. When running DoxyPress instead (using DoxyWizard), I saw an additional entry in the output that included `=delete` and the brief description `Assignment from input iterators is deleted.`  
  
Looking more closely at the source, it seems that the ifndef line is the culprit. I guess this was added as a workaround for the bug?  
  
The definition I saw being documented is the [one that is *not* wrapped with that directive](https://github.com/CPPAlliance/url/blob/3f9954e5596d7cf75c23e3a95eb00f6864c660ae/include/boost/url/params.hpp#L233):  
  
```cpp  
    template<class FwdIt>  
    void  
    assign(FwdIt first, FwdIt last,  
        std::forward_iterator_tag);  
```  
  
So that appears to be behaving correctly (no `=delete`).  
  
Whereas the additional entry that I discovered by running DoxyPress (without it having any awareness of the workaround to hide [the offending code](https://github.com/CPPAlliance/url/blob/3f9954e5596d7cf75c23e3a95eb00f6864c660ae/include/boost/url/params.hpp#L239)) corresponds to this code:  
  
```cpp  
#ifndef BOOST_URL_DOCS  
    /** Assignment from input iterators is deleted  
        @note <!-- Doxygen cannot render ` = delete` -->  
     */  
    template<class FwdIt>  
    void  
    assign(FwdIt first, FwdIt last,  
        std::input_iterator_tag) = delete;  
#endif  
```  
  
My assumption is that the above is getting ignored by Doxygen because of the `#ifndef` directive and that the only reason that directive is there is to hide the current issue being reported. @alandefreitas Is my assessment here correct?  
  
If so, that's good news, as it implies that the issue is actually fixable in docca, and Doxygen is not to blame *per se*. 😄  
  
Now, the "deleted" aspect is not represented in a structured way in the Doxygen XML; it only appears as a suffix (`=delete` ) to the argstring value:  
  
```xml  
<argsstring>(FwdIt first, FwdIt last, std::input_iterator_tag)=delete</argsstring>  
```  
  
But that's okay; we can work with it by looking for that `=delete` suffix, temporarily up-converting it to a structured (element) representation (in docca transformation stage 1), and then rendering it to the final Quickbook format (in docca transformation stage 2).  
  
So my next step is to fix the bug by enhancing the docca XSLT!

---

## Comment 6

> Username: alandefreitas  
> Created at: 2022-03-25 15:49:44 UTC  
> Updated at: 2022-03-25 15:49:53 UTC  
> Url: https://github.com/boostorg/docca/issues/122#issuecomment-1079159859  

> My assumption is that the above is getting ignored by Doxygen because of the #ifndef directive and that the only reason that directive is there is to hide the current issue being reported. @alandefreitas Is my assessment here correct?  
  
Yes. This is correct. The macro is there to circumvent the issue. :)

---

## Comment 7

> Username: evanlenz  
> Created at: 2022-04-14 22:28:57 UTC  
> Url: https://github.com/boostorg/docca/issues/122#issuecomment-1099680047  

@alandefreitas Thank you! Can you verify the fix works as desired in PR #124?

---

## Comment 8

> Username: alandefreitas  
> Created at: 2022-04-14 23:02:35 UTC  
> Url: https://github.com/boostorg/docca/issues/122#issuecomment-1099703410  

Yes. It did work! :partying_face:   
  
![image](https://user-images.githubusercontent.com/5369819/163491032-2a02740a-6fd2-4c0d-8763-2d944562002d.png)
