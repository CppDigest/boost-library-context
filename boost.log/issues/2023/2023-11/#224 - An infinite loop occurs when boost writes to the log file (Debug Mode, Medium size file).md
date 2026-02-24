# #224 - An infinite loop occurs when boost writes to the log file (Debug Mode, Medium size file) [Closed]

> Username: AntonE1986  
> Created at: 2023-11-20 23:04:21 UTC  
> Updated at: 2023-11-21 02:54:24 UTC  
> Closed at: 2023-11-21 00:28:27 UTC  
> Url: https://github.com/boostorg/log/issues/224  

Hello!  
  
It seems issue https://github.com/boostorg/log/issues/209 is not fully fixed with Boost compiled in debug mode:  
  
#209 fix resolve the problem in case of previous log file is empty, for instance because it is not properly rotated:  
![image](https://github.com/boostorg/log/assets/109995044/e027257f-5f9c-4ebc-a9a9-30830ddbfdd1)  
  
Digging deep in to the rotate_file() internals we can find this code, enabled only in debug mode:  
![image](https://github.com/boostorg/log/assets/109995044/9a8c29d8-c1bf-48e6-bb6b-81ab4e1519c3)  
  
As result, in case of log file size before try to publish big log entity was less than minimal rotation size, but with new entity it will be larger than m_FileRotationSize, we will go in the same infinity loop as we had with issue #209

---

## Comment 1

> Username: Lastique  
> Created at: 2023-11-21 00:28:27 UTC  
> Updated at: 2023-11-21 02:54:24 UTC  
> Url: https://github.com/boostorg/log/issues/224#issuecomment-1820023238  

`PLogTargetCollector` on your second screenshot is not part of Boost.Log, I cannot comment on its behavior. Please ask for support from the maintainers of that class.
