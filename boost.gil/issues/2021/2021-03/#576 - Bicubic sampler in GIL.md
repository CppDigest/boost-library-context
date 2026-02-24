# #576 - Bicubic sampler in GIL [Open]

> Username: Scramjet911  
> Created at: 2021-03-13 05:07:30 UTC  
> Updated at: 2021-04-15 16:31:33 UTC  
> Url: https://github.com/boostorg/gil/issues/576  

### Is your feature request related to a problem? Please describe.  
In the current image resizing algorithms in GIL, there is nearest neighbour and bilinear sampler. But bicubic sampler is not available. Bicubic interpolation is also a very popular algorithm which might be needed useful for resizing.  
  
### Describe the solution you'd like  
Implementing a bicubic sampler in sampler.hpp  
  
#### C++ Example  
So the way I have currently implemented it is using [this](https://www.paulinternet.nl/?page=bicubic) as a reference, [here](https://github.com/Scramjet911/gil/blob/6e81dd392b5b67c08fd49e1a63b3b14d9e143e26/include/boost/gil/extension/numeric/sampler.hpp#L210)  
  
But there are issues with my implementation, I cannot figure out why image artifacts are being generated in the output image.  
<div>  
<img src="https://user-images.githubusercontent.com/36035352/111019274-22873300-83e4-11eb-8103-68932a3b08dc.png" width="250">  
<img src="https://user-images.githubusercontent.com/36035352/111019257-07b4be80-83e4-11eb-83d7-417d6523f393.png" width="250">  
</div>  
<div>  
<img src="https://user-images.githubusercontent.com/36035352/111019868-efdf3980-83e7-11eb-9078-a17f6e6fe197.jpg" width="250">  
<img src="https://user-images.githubusercontent.com/36035352/111019877-04233680-83e8-11eb-9fdc-d301e1194d13.jpg" width="250">  
</div>  
Can someone help me with what I am missing?   
  
### Describe alternatives you've considered  
Another method for bicubic interpolation is using convolution like [this](https://en.wikipedia.org/wiki/Bicubic_interpolation#Bicubic_convolution_algorithm)  
  
### Additional context  
Bicubic interpolation is a very popular technique for image interpolation because it is smoother than bilinear interpolation and nearest neighbour interpolation, though it requires higher computational power.  
  
PR #588  
  
Future milestones :  
- [ ] (maybe?) Clean up `sampler.hpp` comments  
- [x] Add tests  
- [ ] Ensure all tests pass

---

## Comment 1

> Username: Sayan-Chaudhuri  
> Created at: 2021-03-29 03:56:27 UTC  
> Url: https://github.com/boostorg/gil/issues/576#issuecomment-809047936  

Has anyone resolved this issue?

---

## Comment 2

> Username: mloskot  
> Created at: 2021-03-29 08:29:57 UTC  
> Url: https://github.com/boostorg/gil/issues/576#issuecomment-809182934  

@Sayan-Chaudhuri If you don't see any related PR or commits or code then the answer is No.

---

## Comment 3

> Username: Scramjet911  
> Created at: 2021-03-29 08:40:10 UTC  
> Url: https://github.com/boostorg/gil/issues/576#issuecomment-809190059  

> Has anyone resolved this issue?  
  
I was working on the testcases, haven't updated it here. will put in a pr soon

---

## Comment 4

> Username: Sayan-Chaudhuri  
> Created at: 2021-03-29 08:51:33 UTC  
> Url: https://github.com/boostorg/gil/issues/576#issuecomment-809197923  

Ok,I got it.

---

## Comment 5

> Username: mloskot  
> Created at: 2021-03-29 08:59:37 UTC  
> Url: https://github.com/boostorg/gil/issues/576#issuecomment-809203886  

@Scramjet911 Please, add the PR `#NNNN` reference to the description of this issue, so it is clear what to follow.

---

## Comment 6

> Username: Scramjet911  
> Created at: 2021-04-15 14:02:37 UTC  
> Url: https://github.com/boostorg/gil/issues/576#issuecomment-820446621  

> @Scramjet911 Please, add the PR `#NNNN` reference to the description of this issue, so it is clear what to follow.  
  
I have added a PR for this issue. Can someone look into it?

---

## Comment 7

> Username: mloskot  
> Created at: 2021-04-15 14:48:59 UTC  
> Url: https://github.com/boostorg/gil/issues/576#issuecomment-820488923  

You've marked it as WIP, means you are still working on it aka not ready for review.

---

## Comment 8

> Username: Scramjet911  
> Created at: 2021-04-15 14:56:37 UTC  
> Url: https://github.com/boostorg/gil/issues/576#issuecomment-820496031  

> You've marked it as WIP, means you are still working on it aka not ready for review.  
  
Oh sorry, I thought WIP meant that it hasn't been reviewed yet, changed it.

---

## Comment 9

> Username: mloskot  
> Created at: 2021-04-15 15:13:38 UTC  
> Updated at: 2021-04-15 15:14:30 UTC  
> Url: https://github.com/boostorg/gil/issues/576#issuecomment-820508792  

See https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#pull-requests  
The WIP is an equivalent of marking a PR as Draft:  
  
- https://github.blog/2019-02-14-introducing-draft-pull-requests/  
- https://github.blog/changelog/2020-04-08-convert-pull-request-to-draft/  
  
The issue with Draft is that   
> Any user with **write access** to the repository can convert a pull request to a draft pull request.  
  
Similarly, marking with [status/work-in-progress](https://github.com/boostorg/gil/labels/status%2Fwork-in-progress) label requires such higher permissions.  
  
So, ad-hoc contributors can just stick `WIP: ` in the PR title.

---

## Comment 10

> Username: Scramjet911  
> Created at: 2021-04-15 16:31:33 UTC  
> Url: https://github.com/boostorg/gil/issues/576#issuecomment-820566032  

> See https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#pull-requests  
> The WIP is an equivalent of marking a PR as Draft:  
>   
>     * https://github.blog/2019-02-14-introducing-draft-pull-requests/  
>   
>     * https://github.blog/changelog/2020-04-08-convert-pull-request-to-draft/  
>   
>   
> The issue with Draft is that  
>   
> > Any user with **write access** to the repository can convert a pull request to a draft pull request.  
>   
> Similarly, marking with [status/work-in-progress](https://github.com/boostorg/gil/labels/status%2Fwork-in-progress) label requires such higher permissions.  
>   
> So, ad-hoc contributors can just stick `WIP: ` in the PR title.  
  
Oh, I see. I added 'WIP:' because I wanted input on how I implemented it and if I should change the way I implemented it.
