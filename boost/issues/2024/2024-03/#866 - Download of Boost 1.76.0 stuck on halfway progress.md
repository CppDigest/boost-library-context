# #866 - Download of Boost 1.76.0 stuck on halfway progress [Open]

> Username: szhang-tyro  
> Created at: 2024-03-01 00:41:09 UTC  
> Updated at: 2024-03-01 02:11:27 UTC  
> Url: https://github.com/boostorg/boost/issues/866  

I have a react-native project and ran `pod install` to install all dependencies, but the downloading progress for boost 1.76.0 hangs on 49%.  
  
I then tried manually download the library from [boost.org website](https://www.boost.org/users/history/version_1_76_0.html), got the same problem as shown in screenshots below.   
  
Could this be some issue with the host server?  
  
<img width="318" alt="image" src="https://github.com/boostorg/boost/assets/140478865/16355b1d-f6cf-403d-8b95-e1b5ddb39b0e">  
  
<img width="608" alt="image" src="https://github.com/boostorg/boost/assets/140478865/06a0fc76-b97f-43d7-afda-97ee0a81c016">

---

## Comment 1

> Username: sdarwin  
> Created at: 2024-03-01 01:13:55 UTC  
> Url: https://github.com/boostorg/boost/issues/866#issuecomment-1972265912  

>  a react-native project  
  
I believe currently boost.org and react-native are downloading from two distinct separate locations.  Therefore it's an interesting data point to know if you're able to download from one, but not the other.  However, you're saying they both hang midway?  
What is your typical download bandwidth?  Which world region / timezone?

---

## Comment 2

> Username: szhang-tyro  
> Created at: 2024-03-01 02:11:26 UTC  
> Url: https://github.com/boostorg/boost/issues/866#issuecomment-1972326889  

Hi thanks for the reply.  
  
Not so sure about my bandwidth, the world region/timezone is in Australia/Sydney( AEDT ). This downloading problem has been found since yesterday and we've tested on difference devices with different internet connections. Problem persisted.
