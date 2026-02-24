# #135 - very confused output, and unsure why [Open]

> Username: vinniefalco  
> Created at: 2022-09-11 17:10:19 UTC  
> Updated at: 2022-09-25 07:56:16 UTC  
> Url: https://github.com/boostorg/docca/issues/135  

In this branch:  
https://github.com/vinniefalco/url/tree/docca  
  
Some very weird things are happening. For example here is the member declaration for `userinfo()`:  
https://github.com/vinniefalco/url/blob/4a8df7369af8946a56ee8ff88b599b4cc5b3c544/include/boost/url/url_view_base.hpp#L633  
  
And yet it is being rendered as a static member:  
![image](https://user-images.githubusercontent.com/1503976/189540112-7982ca87-ba84-450d-8780-3180176d9cda.png)  
  
And a regular member:  
![image](https://user-images.githubusercontent.com/1503976/189540133-cbbc5c8d-65c4-4370-b2ce-afd120db70f7.png)  
  
Something similar is happening to `string_view_base`. These operators are declared as friends (free functions):  
https://github.com/vinniefalco/url/blob/4a8df7369af8946a56ee8ff88b599b4cc5b3c544/include/boost/url/grammar/string_view_base.hpp#L783  
  
But they are showing up as static members, protected DATA members, and regular members at the same time (lol)!  
![image](https://user-images.githubusercontent.com/1503976/189540222-2ee9d100-b6b9-489e-81d3-e86e164f7f6b.png)  
  
![image](https://user-images.githubusercontent.com/1503976/189540224-4271fce9-1149-4f25-ad3a-2d1188d8d1b2.png)  
  
![image](https://user-images.githubusercontent.com/1503976/189540220-754eacfa-9409-46ee-bac3-f5d6c172b972.png)  
  
Something ain't right :)

---

## Comment 1

> Username: sehe  
> Created at: 2022-09-12 12:57:27 UTC  
> Url: https://github.com/boostorg/docca/issues/135#issuecomment-1243702725  

This is looking normal for me on my superboost install @develop with (boost url from the new master submodule.  
  
The key difference seems to be that you added [grouping](https://doxygen.nl/manual/grouping.html#memgroup)¹.  
  
Spelunking through the XSL I spot this:   
  
```xsl  
  <!--  
    ASSUMPTION (for now): At least one member per section (table) must not be in a user-defined group.  
    Also, we may need a more robust mapping between a user-defined group's members and the sections  
    in which they belong. For now, we are using this partial test.  
  -->  
```  
  
All in all it seems like there might be a known issue around mxing user-defined groups with the automatic section grouping.  
  
Perhaps it might be helpful to define an explicit group (@defgroup/@ingroup). I couldn't immediately make it work though.  
  
------  
  
¹ Keep in mind that Docca [stipulates](https://github.com/boostorg/docca#usage) that `DISTRIBUTE_GROUP_DOC = YES` be set

---

## Comment 2

> Username: evanlenz  
> Created at: 2022-09-13 18:59:02 UTC  
> Url: https://github.com/boostorg/docca/issues/135#issuecomment-1245836899  

I'm taking a look. Thank you @sehe for your investigation so far!

---

## Comment 3

> Username: evanlenz  
> Created at: 2022-09-25 07:36:10 UTC  
> Url: https://github.com/boostorg/docca/issues/135#issuecomment-1257139445  

I've been having trouble reproducing this behavior. I have the "docca" branch checked out from vinniefalco/url...  
  
I can see why it's happening from the XSLT code, but without being able to get the same Doxygen XML as input, it's hard to properly diagnose & fix. I'm not sure what I'm doing differently.

---

## Comment 4

> Username: evanlenz  
> Created at: 2022-09-25 07:56:16 UTC  
> Url: https://github.com/boostorg/docca/issues/135#issuecomment-1257141982  

My hypothesis is that docca never did properly support user-defined groups and that when I wrote that code (including the comment above) I didn't even know what they were exactly. Also, my suspicion is that Doxygen is not giving us all the info we need (but I'll bracket that for now, because I'm not sure).  
  
@vinniefalco Let me ask you this. What is your intention for using grouping here? If it's not intentional, then all you have to do is delete lines 627 and 641 from here to make the problem go away (I predict): https://github.com/vinniefalco/url/blob/4a8df7369af8946a56ee8ff88b599b4cc5b3c544/include/boost/url/url_view_base.hpp#L627  
  
I still want to make sure we properly support grouping, but I'm wondering if this workaround is an easy "fix" for you in the mean time? 😅
