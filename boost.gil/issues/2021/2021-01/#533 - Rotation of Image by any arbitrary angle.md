# #533 - Rotation of Image by any arbitrary angle [Closed]

> Username: gautam-dev-maker  
> Created at: 2021-01-03 12:53:12 UTC  
> Updated at: 2022-04-29 12:13:33 UTC  
> Closed at: 2022-04-29 12:13:19 UTC  
> Url: https://github.com/boostorg/gil/issues/533  

### Is your feature request related to a problem? Please describe.  
  
The rotation function currently available only rotates by absolute angles ie 90,180,270. So  a proper function is needed to rotate the image by any arbitrary angle.  
  
### Describe the solution you'd like  
  
The solution to this problem is using affine transformation or shear transformation to rotate the image  
  
![image](https://user-images.githubusercontent.com/64469823/103479032-9dced600-4df0-11eb-86bc-31474b5c2121.png)

---

## Comment 1

> Username: gautam-dev-maker  
> Created at: 2021-01-05 04:49:13 UTC  
> Url: https://github.com/boostorg/gil/issues/533#issuecomment-754393283  

May I take up this task?

---

## Comment 2

> Username: mloskot  
> Created at: 2021-01-05 06:59:49 UTC  
> Updated at: 2021-02-12 08:42:56 UTC  
> Url: https://github.com/boostorg/gil/issues/533#issuecomment-754443403  

You're more than welcome to.  
No need to ask for permissions - if you don't see any related PR opened, then assume nobody is working on it - features are reviewed and accepted in FIFO manner.  
If you have any questions then please ask.

---

## Comment 3

> Username: sdebionne  
> Created at: 2021-01-05 17:14:47 UTC  
> Url: https://github.com/boostorg/gil/issues/533#issuecomment-754772667  

@gautam-dev-maker I would advice to check [this example](https://github.com/boostorg/gil/blob/develop/example/affine.cpp) first, affine transformations are already implemented -but might not be optimal.

---

## Comment 4

> Username: gkumar28  
> Created at: 2021-02-11 01:00:12 UTC  
> Url: https://github.com/boostorg/gil/issues/533#issuecomment-777142845  

Is there someone still working on this?

---

## Comment 5

> Username: mloskot  
> Created at: 2021-02-12 08:43:15 UTC  
> Updated at: 2021-02-12 08:43:24 UTC  
> Url: https://github.com/boostorg/gil/issues/533#issuecomment-778059881  

@gkumar28 As explained in https://github.com/boostorg/gil/issues/533#issuecomment-754443403  
No need to ask for permissions - if you don't see any related PR opened, then assume nobody is working on it - features are reviewed and accepted in FIFO manner.

---

## Comment 6

> Username: Scramjet911  
> Created at: 2021-02-16 14:58:20 UTC  
> Url: https://github.com/boostorg/gil/issues/533#issuecomment-779891669  

> Is there someone still working on this?  
>   
  
@gkumar28 Affine transformation already exist in an example as @sdebionne said. Optimization might be possible though.

---

## Comment 7

> Username: gkumar28  
> Created at: 2021-02-27 21:21:16 UTC  
> Url: https://github.com/boostorg/gil/issues/533#issuecomment-787148497  

Sorry for such a late reply.   
I have opened a PR for this issue. #565   
Please review it.

---

## Comment 8

> Username: marco-langer  
> Created at: 2022-04-29 09:09:33 UTC  
> Url: https://github.com/boostorg/gil/issues/533#issuecomment-1113085916  

The PR is merged and the issue completed, or am I missing something important?

---

## Comment 9

> Username: mloskot  
> Created at: 2022-04-29 12:13:19 UTC  
> Url: https://github.com/boostorg/gil/issues/533#issuecomment-1113241568  

@marco-langer No, I don't think you are missing anything. Good catch, thanks.  
It is the commit message merging the #565 that missed the `Closes #533` line.
