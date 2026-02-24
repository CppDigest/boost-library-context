# #307 [string] Add a c_str() method [Merged]

> Username: ldionne  
> Created at: 2016-11-07 17:49:48 UTC  
> Updated at: 2016-11-09 01:03:48 UTC  
> Merged at: 2016-11-09 01:03:29 UTC  
> Closed at: 2016-11-09 01:03:29 UTC  
> Url: https://github.com/boostorg/hana/pull/307  



---

## Comment 1

> Username: ricejasonf  
> Created_at: 2016-11-07 18:49:55 UTC  
> Url: https://github.com/boostorg/hana/pull/307#issuecomment-258925713  

Does adding the storage to the body of `hana::string` affect the compile-time of creating strings?  
  
I might have some time to test this out if you haven't already. ☕️

---

## Comment 2

> Username: ldionne  
> Created_at: 2016-11-07 18:51:01 UTC  
> Url: https://github.com/boostorg/hana/pull/307#issuecomment-258926010  

Good point; I don't know for sure. It would be great if you had time to check it out, I'm at the committee meeting right now.

---

## Comment 3

> Username: ricejasonf  
> Created_at: 2016-11-08 05:51:56 UTC  
> Url: https://github.com/boostorg/hana/pull/307#issuecomment-259052016  

![1478583691514971298](https://cloud.githubusercontent.com/assets/2257044/20088193/6b8dbd10-a533-11e6-82fc-7a90fe442b58.jpg)  
  
```  
  <% (0..n).each do |i| %>  
  
    auto x<%= i %> = string<<%= ([i % 128] * 100).join(', ') %>>{};  
  
  <% end %>  
```  
  
It wasn't a perfect test, but it looks like the difference is trivial.

---

## Comment 4

> Username: ldionne  
> Created_at: 2016-11-08 14:55:26 UTC  
> Url: https://github.com/boostorg/hana/pull/307#issuecomment-259157753  

Thanks a lot for looking into it! Here's what I tried: https://gist.github.com/ldionne/bf105d0ee029774677ef55c3e2e409c1.  
  
And the results:  
<img width="1277" alt="screen shot 2016-11-08 at 06 54 34" src="https://cloud.githubusercontent.com/assets/700834/20103793/43163446-a580-11e6-9072-e707f763cc7b.png">  
  
So I'll use the second implementation, which seems to be a bit faster.

---
