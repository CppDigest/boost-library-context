# #869 - natvis is broken [Open]

> Username: vinniefalco  
> Created at: 2023-03-09 02:45:50 UTC  
> Updated at: 2024-03-01 13:25:18 UTC  
> Url: https://github.com/boostorg/json/issues/869  

Natvis: C:\Users\Vinnie\src\boost\libs\json\include\boost\json\json.natvis(58,4): Warning: Unable to load a visualized preview for type 'boost::json::string' because the conditions of all <DisplayString> elements were false.  
Natvis: C:\Users\Vinnie\src\boost\libs\json\include\boost\json\json.natvis(58,4): Warning: Unable to load a visualized preview for type 'boost::json::string' because the conditions of all <DisplayString> elements were false.  
Natvis: C:\Users\Vinnie\src\boost\libs\json\include\boost\json\json.natvis(58,4): Warning: Unable to load a visualized preview for type 'boost::json::string' because the conditions of all <DisplayString> elements were false.

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-03-09 09:28:03 UTC  
> Url: https://github.com/boostorg/json/issues/869#issuecomment-1461648769  

I don't think I've touched internal representation for any of the containers, and I definitely haven't touched the natvis file. So, I have to ask for a reproducible example.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2023-03-09 15:14:16 UTC  
> Url: https://github.com/boostorg/json/issues/869#issuecomment-1462227733  

well try inspecting any json::value

---

## Comment 3

> Username: Aspire-Design-Ltd  
> Created at: 2024-02-24 19:42:09 UTC  
> Updated at: 2024-02-24 19:44:02 UTC  
> Url: https://github.com/boostorg/json/issues/869#issuecomment-1962636148  

Hi. I am no expert but I have been trying to learn Boost JSON using VSCODE, The json.natvis file does not work but I think this is due to changes made to VSCODE. I have made changes to the natvis file which are working for me although I could not test the kind::string+64 as I could not see how to initiate a value.  
It seems VSCODE has broken how char array can be viewed so I have converted to viewing a null terminated string.  
Again I am no expert so I may be hacking rather than correcting the issue.  
MPC.  
  
PS I could not get rid of the pointers to the null terminated strings.  
  
[json.natvis.txt](https://github.com/boostorg/json/files/14394227/json.natvis.txt)  
  
  
![Screenshot from 2024-02-24 19-41-09](https://github.com/boostorg/json/assets/146966616/fe535908-3471-45fe-a857-8e776016c15d)

---

## Comment 4

> Username: grisumbras  
> Created at: 2024-03-01 13:25:17 UTC  
> Url: https://github.com/boostorg/json/issues/869#issuecomment-1973199567  

So, you have a fix to at least some of the issues with the natvis file? That's great! Please create a PR.
