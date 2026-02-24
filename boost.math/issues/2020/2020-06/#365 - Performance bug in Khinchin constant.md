# #365 - Performance bug in Khinchin constant [Closed]

> Username: NAThompson  
> Created at: 2020-06-06 01:04:04 UTC  
> Updated at: 2021-11-03 17:53:32 UTC  
> Closed at: 2021-11-03 17:53:32 UTC  
> Url: https://github.com/boostorg/math/issues/365  

Most constants recover 500 bits of precision in approximately ~500ns. However, the Khinchin constant takes seconds at this precision and minutes at only 1000 bits:  
  
```  
Khinchin/512         9592598 us      6492850 us            1  
Khinchin/1024       57714073 us     47416656 us            1  
```  
  
[Here](https://www.davidhbailey.com/dhbpapers/khinchine.pdf) is a paper presenting some high precision evaluations of this constant.  
  
  
See [here](https://github.com/boostorg/math/pull/366) for the file which demos these numbers.  
  
Might it be reasonable to do ~2000 digits from a string constructor?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-06-06 08:00:31 UTC  
> Url: https://github.com/boostorg/math/issues/365#issuecomment-640008644  

We might have to do that - it's hard to compute as noted in https://mathworld.wolfram.com/KhinchinsConstant.html

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-06-06 13:11:01 UTC  
> Url: https://github.com/boostorg/math/issues/365#issuecomment-640059092  

Ok, cool, I'll find a cached value and see what I can do with it. . .

---

## Comment 3

> Username: ckormanyos  
> Created at: 2020-06-07 18:48:19 UTC  
> Url: https://github.com/boostorg/math/issues/365#issuecomment-640262498  

I optimized that a few years back in another project.  
I can look if i still have my notes or the optimized version.  
  
In my previous work on _e_float_, I used...  
  
```  
  // 1100 digits of Khinchin's constant  
  static const std::string str =  
    std::string("2.")  
  + std::string("6854520010653064453097148354817956938203822939944629530511523455572188595371520028011411749318476979")  
  + std::string("9515346590528809008289767771641096305179253348325966838185231542133211949962603932852204481940961806")  
  + std::string("8664166428930847788062036073705350103367263357728904990427070272345170262523702354581068631850103237")  
  + std::string("4655803775026442524852869468234189949157306618987207994137235500057935736698933950879021244642075289")  
  + std::string("7414591476930184490506017934993852254704042033779856398310157090222339100002207725096513324604444391")  
  + std::string("9169146085968234821283246228292710126906974182348477675457348986254203392662351862086778136650969658")  
  + std::string("3146995271837448054012195366666049648269890827548115254721177330319675947383719393578106059230401890")  
  + std::string("7113496246737068412217946810740608918276695667117166837405904739368809534504899970471763904513432323")  
  + std::string("7715103219651503824698888324870935399469608264781812056634946712578436664579740977848366204977774868")  
  + std::string("2765697087163192938512899314199518611673792654620563505951385713761697126872299805327673278710513763")  
  + std::string("9563719023145289003058136910904799672757571385043565050641590820999623402779053834180985121278529455")  
 ;  
```
