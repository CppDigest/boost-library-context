# #813 - List of supported compilers is out of date [Closed]

> Username: mloskot  
> Created at: 2021-02-17 14:17:24 UTC  
> Updated at: 2021-02-19 18:55:35 UTC  
> Closed at: 2021-02-19 18:55:35 UTC  
> Url: https://github.com/boostorg/geometry/issues/813  

Following [report on the ML](https://lists.boost.org/geometry/2021/02/3834.php) the task is to  
- either update the list at  https://www.boost.org/doc/libs/1_75_0/libs/geometry/doc/html/geometry/compilation.html and make it in sync with the compilers currently covered by CI builds,  
- or simply remove the whole list and assume the C++14 requirement is sufficient documentation  
  
The CI builds are available from the README, so everyone can find out what compilers/versions are being tested.  
  
This info is a kind of a moving target and it may be difficult to keep up to date in the docs, so I'd opt for the removal.  
  
What do you think @barendgehrels @awulkiew @vissarion ?

---

## Comment 1

> Username: awulkiew  
> Created at: 2021-02-17 15:26:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/813#issuecomment-780633151  

@mloskot I'm ok with removal and assuming that C++14 requirement is enough.

---

## Comment 2

> Username: vissarion  
> Created at: 2021-02-17 16:58:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/813#issuecomment-780699832  

@mloskot I am also ok with the simplest solution, i.e. remove and assume the C++14 requirement is sufficient.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2021-02-19 16:37:36 UTC  
> Url: https://github.com/boostorg/geometry/issues/813#issuecomment-782190292  

@mloskot I'm also fine with just `C++14`
