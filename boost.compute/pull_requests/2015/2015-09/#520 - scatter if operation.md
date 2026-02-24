# #520 scatter if operation [Merged]

> Username: jpola  
> Created at: 2015-09-28 15:24:12 UTC  
> Updated at: 2015-10-02 03:40:40 UTC  
> Merged at: 2015-10-02 03:40:39 UTC  
> Closed at: 2015-10-02 03:40:39 UTC  
> Url: https://github.com/boostorg/compute/pull/520  

Hi,  
  
In my project I needed the scatter if operation so I thought I will also push it here. Could you please check it if this is useful?  
  
Thanks,

---

## Comment 1

> Username: jszuppe  
> Created_at: 2015-09-28 19:12:37 UTC  
> Updated_at: 2015-09-29 07:28:03 UTC  
> Url: https://github.com/boostorg/compute/pull/520#discussion_r40592656  

You should put your name here ;)

---

## Comment 2

> Username: jszuppe  
> Created_at: 2015-09-28 19:12:47 UTC  
> Updated_at: 2015-09-29 07:28:03 UTC  
> Url: https://github.com/boostorg/compute/pull/520#discussion_r40592673  

and here!

---

## Comment 3

> Username: kylelutz  
> Created_at: 2015-09-29 02:58:43 UTC  
> Updated_at: 2015-09-29 02:59:00 UTC  
> Url: https://github.com/boostorg/compute/pull/520#issuecomment-143929452  

Looks great, thanks for adding this!  
  
One issue with initialization in the tests (from the clang build in travis-ci):  
  
```  
/home/travis/build/boostorg/compute/test/test_scatter_if.cpp:38:21: error:   
      non-aggregate type 'bc::vector<int>' cannot be initialized with an  
      initializer list  
    bc::vector<int> output = {-1, -1, -1, -1, -1, -1, -1, -1, -1, -1};  
                    ^        ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
However, you should be able to change this to use the size and constant constructor like so:  
  
```  
bc::vector<int> output(input.size, -1, queue);  
```

---

## Comment 4

> Username: kylelutz  
> Created_at: 2015-10-02 03:40:40 UTC  
> Url: https://github.com/boostorg/compute/pull/520#issuecomment-144909508  

Merged! Thanks!

---
