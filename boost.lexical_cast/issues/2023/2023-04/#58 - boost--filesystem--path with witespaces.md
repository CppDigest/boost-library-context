# #58 - boost::filesystem::path with witespaces [Closed]

> Username: ohhmm  
> Created at: 2023-04-13 13:30:09 UTC  
> Updated at: 2024-03-23 18:23:48 UTC  
> Closed at: 2024-03-23 18:23:48 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/58  

boost::filesystem::path is being deserialized to the first whitespace occurrence.

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-02-13 08:50:53 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/58#issuecomment-1940790314  

Could you please provide a reproducer?

---

## Comment 2

> Username: ohhmm  
> Created at: 2024-02-13 19:15:09 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/58#issuecomment-1942217509  

C:\Program Files on Windows deserialized to C:\Program

---

## Comment 3

> Username: apolukhin  
> Created at: 2024-02-15 07:24:26 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/58#issuecomment-1945498750  

@ohhmm to what type are you deserializing? Is that std::string?

---

## Comment 4

> Username: apolukhin  
> Created at: 2024-02-15 07:36:39 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/58#issuecomment-1945511135  

Is the path quoted or not? All the above cases are covered with tests:  
  
https://github.com/boostorg/lexical_cast/blob/3433c34b43969f2d3fc7309d895ef66019e3cfc1/test/filesystem_test.cpp#L35-L51  
  
What version of Boost are you using?

---

## Comment 5

> Username: ohhmm  
> Created at: 2024-02-20 20:18:24 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/58#issuecomment-1954989262  

I reproduced it using boost::program_options where option type is boost::filesystem::path. Tried it with multiple versions of boost on Windows. Tried with both quotations enabled/disabled.

---

## Comment 6

> Username: apolukhin  
> Created at: 2024-02-22 06:19:26 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/58#issuecomment-1958778361  

@ohhmm is your case not covered by the tests in  
https://github.com/boostorg/lexical_cast/blob/3433c34b43969f2d3fc7309d895ef66019e3cfc1/test/filesystem_test.cpp#L35-L51  
  
?  
  
If not covered, please provide a reproducer
