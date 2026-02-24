# #825 - boost::json::serialize returns NULL string [Closed]

> Username: marincovic  
> Created at: 2022-12-22 10:28:10 UTC  
> Updated at: 2023-06-01 08:44:32 UTC  
> Closed at: 2023-06-01 08:44:32 UTC  
> Url: https://github.com/boostorg/json/issues/825  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
Hello Team,  
I am having issues when trying to serialize my JSON into a string before sending it.  
The problem is that the serialize command returns a NULL string.  
From what I can see the string is not empty it only say unable to read memory.  
![image](https://user-images.githubusercontent.com/12939900/209114314-413e199f-75b1-4831-8f48-3fde2863bbfd.png)  
  
  
### Version of Boost  
  
1.81.0  
  
### Steps necessary to reproduce the problem  
All elements in the map are std::string.  
```  
	boost::json::object JSONObject;  
	JSONObject["type"] = this->PV_DTOMessageType;  
	for (auto iter = keyValueMap.begin(); iter != keyValueMap.end(); ++iter) {  
		JSONObject[iter->first] = iter->second;  
	}  
	return boost::json::serialize(JSONObject);  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-12-22 11:03:01 UTC  
> Url: https://github.com/boostorg/json/issues/825#issuecomment-1362702456  

What is a "NULL string"? I'm pretty sure we can't create an invalid `std::string`. Can you create a minimal reproducible example, so that I can investigate your issue?

---

## Comment 2

> Username: marincovic  
> Created at: 2022-12-27 14:00:28 UTC  
> Updated at: 2022-12-27 14:48:55 UTC  
> Url: https://github.com/boostorg/json/issues/825#issuecomment-1365921983  

Hello,  
  
I did some testing... When I am using VS 2022 and when I use the in Visual Studio platform toolset everything works fine.  
When I switch to LLVM (clang-cl 15.0.1) the system does not automatically copy the dll so I manually transferred the dll to the Debug and then the issue occurs.  
I also tried to switch to header only but then I get a duplicate symbol issue.  
  
While testing it looks like the pointer of the std::string is nullptr:  
![image](https://user-images.githubusercontent.com/12939900/209677210-f5897623-4cdb-46a0-aa41-dce831134845.png)  
  
where the ```tempString``` value is:  
![image](https://user-images.githubusercontent.com/12939900/209677292-5a5ac841-791e-47de-acca-e345f534e0e7.png)  
  
  
The Code that I am using is:  
```  
std::string Namespace::GenerateJSON(std::map<std::string, std::string> keyValueMap)  
{  
	boost::json::object JSONObject;  
	JSONObject.emplace("type", this->PV_DTOMessageType);  
	std::string tempString;  
	auto iter = keyValueMap.begin();  
	tempString = boost::json::serialize(JSONObject);  
	for (; iter != keyValueMap.end(); ++iter) {  
		JSONObject.emplace(iter->first,iter->second);  
		tempString = boost::json::serialize(JSONObject);  
	}  
	tempString = boost::json::serialize(JSONObject);  
	return tempString;  
}  
```  
NOTE: I used vcpkg install to add the library (I did it the same for beast and everything worked fine)

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-01-02 11:44:41 UTC  
> Url: https://github.com/boostorg/json/issues/825#issuecomment-1368874145  

Wait, do I understand you correctly that you build Boost.JSON for one platform, but build your program that links to Boost.JSON for another platform? If you do — don't. That's never guaranteed to work. And even if it seems to work, most likely it silently has errors.

---

## Comment 4

> Username: grisumbras  
> Created at: 2023-05-31 12:20:20 UTC  
> Url: https://github.com/boostorg/json/issues/825#issuecomment-1570122515  

@marincovic have you figured out why you were having this error?

---

## Comment 5

> Username: marincovic  
> Created at: 2023-05-31 14:26:59 UTC  
> Url: https://github.com/boostorg/json/issues/825#issuecomment-1570345517  

Hello,  
  
Sorry I did not post it anything sooner. I could not resolve the issue at all, so I had to switch JSON tools. From what I remember the issue was connected to the DLL that was being used (I tried building it with Clang-cl as well but there was still an issue).  
Unfortunately it was some time ago so I am not 100% sure.  
  
Once again sorry for the late response.  
BR//  
Marin

---

## Comment 6

> Username: grisumbras  
> Created at: 2023-06-01 08:44:32 UTC  
> Url: https://github.com/boostorg/json/issues/825#issuecomment-1571615779  

It seems like the issue was caused by using an incompatible binary. I'm closing this for now.
