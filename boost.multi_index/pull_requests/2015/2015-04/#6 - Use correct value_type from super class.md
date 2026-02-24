# #6 Use correct value_type from super class. [Closed]

> Username: jhunold  
> Created at: 2015-04-22 07:57:02 UTC  
> Updated at: 2015-04-22 10:23:48 UTC  
> Closed at: 2015-04-22 10:23:48 UTC  
> Url: https://github.com/boostorg/multi_index/pull/6  

error: use of undeclared identifier 'value_type'  
  
Happens in c++11 mode only.

---

## Comment 1

> Username: joaquintides  
> Created_at: 2015-04-22 10:23:48 UTC  
> Url: https://github.com/boostorg/multi_index/pull/6#issuecomment-95118384  

Hi Jürgen, thank you, I've applied a reformulation of your patch that is more in line with the coding conventions of the lib:  
  
https://github.com/boostorg/multi_index/commit/3f8d49acea7857383dbae904048d75f4c2099b51

---
