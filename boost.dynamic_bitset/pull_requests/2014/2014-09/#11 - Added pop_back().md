# #11 Added pop_back(). [Closed]

> Username: ricky65  
> Created at: 2014-09-01 13:43:24 UTC  
> Updated at: 2014-12-18 14:43:42 UTC  
> Closed at: 2014-09-15 15:32:20 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/11  

Added a pop_back() member function which decreases the size of the bitset by one.

---

## Comment 1

> Username: mclow  
> Created_at: 2014-09-02 02:51:08 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/11#issuecomment-54104551  

First thing; we don't merge to master. We merge to develop, let the tests cycle, and then merge from develop to master.  
  
Second thing: I don't see any tests here.

---

## Comment 2

> Username: mclow  
> Created_at: 2014-09-02 02:53:40 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/11#issuecomment-54104671  

Third: Where is the documentation?

---

## Comment 3

> Username: ricky65  
> Created_at: 2014-09-02 23:49:43 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/11#issuecomment-54234549  

Hi. Sorry, I made a mistake. The tests and documentation should now be included.

---

## Comment 4

> Username: mclow  
> Created_at: 2014-09-03 02:22:53 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/11#issuecomment-54244351  

Question: What happens if you call pop_back on an empty bitset?

---

## Comment 5

> Username: ricky65  
> Created_at: 2014-09-03 14:22:27 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/11#issuecomment-54304398  

vector::pop_back is called which AFAIK is undefined behaviour.

---

## Comment 6

> Username: mclow  
> Created_at: 2014-09-03 14:25:48 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/11#issuecomment-54304920  

I think that's worth a mention in the documentation, don't you?  
something like:  
       Precondition: bitset is not empty;

---

## Comment 7

> Username: ricky65  
> Created_at: 2014-09-03 14:52:32 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/11#issuecomment-54308993  

I agree. I will update the documentation accordingly.

---

## Comment 8

> Username: ricky65  
> Created_at: 2014-09-03 15:16:59 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/11#issuecomment-54312637  

I have added "Precondition: !this->empty().".

---

## Comment 9

> Username: mclow  
> Created_at: 2014-09-15 15:32:20 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/11#issuecomment-55607811  

I merged this to develop as commit 225064d3558ced62db63ce23e108b85af2f09d07.  
Please watch the test results and ping me in a few days, and I will merge to master.

---

## Comment 10

> Username: ricky65  
> Created_at: 2014-09-18 00:54:44 UTC  
> Updated_at: 2014-09-18 00:55:25 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/11#issuecomment-55982375  

Thanks. Sorry again for the mess up.   
  
I have also implemented other functions - capacity(), reserve(), and shrink_to_fit(). I find these useful and am surprised by their omission. I'd be happy to include them if there is interest. Perhaps there are reasons why they're not implemented?

---

## Comment 11

> Username: mclow  
> Created_at: 2014-09-18 17:37:44 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/11#issuecomment-56074856  

> I have also implemented other functions - capacity(), reserve(), and shrink_to_fit(). I find these useful and am surprised by their omission. I'd be happy to include them if there is interest. Perhaps there are reasons why they're not implemented?  
  
I would suggest starting a discussion on the boost-developer's mailing list.  
I have no objection to adding them to the library.

---

## Comment 12

> Username: ricky65  
> Created_at: 2014-09-20 12:37:28 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/11#issuecomment-56266722  

Thanks. I'll start a discussion about it.

---

## Comment 13

> Username: ricky65  
> Created_at: 2014-12-17 17:18:10 UTC  
> Updated_at: 2014-12-18 14:43:42 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/11#issuecomment-67357496  

Hi Marshall,  
I submitted a patch for capacity(), reserve(), and shrink_to_fit() a while ago on GitHub and it is still waiting in a Pull Request. Link: https://github.com/boostorg/dynamic_bitset/pull/12  
I would be grateful if you could take a look at it.  
Thanks,  
Riccardo

---
