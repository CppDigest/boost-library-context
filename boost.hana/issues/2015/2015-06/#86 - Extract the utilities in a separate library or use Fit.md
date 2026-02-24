# #86 - [Functional] Extract the utilities in a separate library or use Fit? [Closed]

> Username: ldionne  
> Created at: 2015-06-07 15:42:03 UTC  
> Updated at: 2015-08-09 06:55:26 UTC  
> Closed at: 2015-08-09 06:55:26 UTC  
> Url: https://github.com/boostorg/hana/issues/86  

The observation is that Hana's Functional module is basically the [Fit](http://github.com/pfultz2/fit) library by @pfultz2. It might be nice to use Fit instead of our own mini sub-library to increase code reuse. Of course, adding a dependency is really annoying, but there might be a good way to do it.  
  
@pfultz2 What do you think? Also, do you plan on submitting Fit for Boost review?

---

## Comment 1

> Username: viboes  
> Created at: 2015-06-07 20:26:11 UTC  
> Url: https://github.com/boostorg/hana/issues/86#issuecomment-109797416  

I believed that both Tick and Fit were already on http://www.boost.org/community/review_schedule.html.  
Paul if you add them I could manage the review of one.

---

## Comment 2

> Username: pfultz2  
> Created at: 2015-06-07 21:56:36 UTC  
> Url: https://github.com/boostorg/hana/issues/86#issuecomment-109802775  

> What do you think?  
  
Its very possible. I dont know how the logistics will work since your library will go to review first.  
  
> Also, do you plan on submitting Fit for Boost review?  
  
Yes, there is some more work I hope to finish in the next month, and then I would like to ask for an informal review first, and then move towards a scheduling a formal review.

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-06-07 21:58:53 UTC  
> Url: https://github.com/boostorg/hana/issues/86#issuecomment-109802853  

> I believed that both Tick and Fit were already on http://www.boost.org/community/review_schedule.html.  
> Paul if you add them I could manage the review of one.  
  
I am not sure how I get added to the schedule. It looks like the Fit library is reaching much closer to formal review. If you are able to be the review manager that would be great.

---

## Comment 4

> Username: ldionne  
> Created at: 2015-06-07 22:00:59 UTC  
> Url: https://github.com/boostorg/hana/issues/86#issuecomment-109802930  

> > What do you think?  
>   
> Its very possible. I dont know how the logistics will work since your library will go to review first.  
  
Oh, I don't see the "Functional" utilities as really part of Hana. I mean, they are, but they are definitely not core functionality. As far as I'm concerned they could be moved to the `detail` namespace, except that I would need to modify the examples that use them. I don't see Hana's "Functional" module as any kind of threat to Fit entering Boost, if Hana is accepted.

---

## Comment 5

> Username: pfultz2  
> Created at: 2015-06-07 22:41:43 UTC  
> Url: https://github.com/boostorg/hana/issues/86#issuecomment-109808980  

> As far as I'm concerned they could be moved to the detail namespace, except that I would need to modify the examples that use them.  
  
How would the examples be modified? Would they use Fit or the `detail` namespace?  
  
I think it is good idea for us to coordinate it together, especially since there can be subtle differences(like `hana::compose` vs `fit::compose`).

---

## Comment 6

> Username: ldionne  
> Created at: 2015-06-08 18:43:25 UTC  
> Url: https://github.com/boostorg/hana/issues/86#issuecomment-110103750  

> How would the examples be modified? Would they use Fit or the detail namespace?  
  
Yes, basically. Not a large modification.  
  
> I think it is good idea for us to coordinate it together, especially since there can be subtle differences(like hana::compose vs fit::compose).  
  
I think so too. To give us some more time, I'll  move the Functional module to the Details module of the documentation, so it is not advertised as being part of the Boost submission. What do you think?

---

## Comment 7

> Username: pfultz2  
> Created at: 2015-06-08 19:04:15 UTC  
> Url: https://github.com/boostorg/hana/issues/86#issuecomment-110108868  

> I think so too. To give us some more time, I'll move the Functional module to the Details module of the documentation, so it is not advertised as being part of the Boost submission. What do you think?  
  
Sounds good. Perhaps, there should be a note in the review that you plan to use Fit for the functional utilities, so the reviewers understand the use of the `detail` namespace in the examples.

---

## Comment 8

> Username: ldionne  
> Created at: 2015-06-08 19:08:05 UTC  
> Url: https://github.com/boostorg/hana/issues/86#issuecomment-110109595  

> Sounds good. Perhaps, there should be a note in the review that you plan to use Fit for the functional utilities, so the reviewers understand the use of the detail namespace in the examples.  
  
I did not move the functional utilities to the `detail::` namespace, but only to the "Details" part of the documentation, upon which people must not rely. So there's no usage of `detail::` in the few examples that use the functional utilities. However, I'll put a note in the documentation of that module that it might be replaced by Fit, which is why it's kinda hidden right now.

---

## Comment 9

> Username: viboes  
> Created at: 2015-06-13 07:57:25 UTC  
> Url: https://github.com/boostorg/hana/issues/86#issuecomment-111685640  

Louis, where have you added this note?

---

## Comment 10

> Username: viboes  
> Created at: 2015-06-13 08:02:10 UTC  
> Url: https://github.com/boostorg/hana/issues/86#issuecomment-111685782  

@pfultz2 Paul let me know when you consider your library is ready for review and why not some dates would be good for you. I will need to do a check then so that we can add it to the review schedule.  
Please let follow this privately.

---

## Comment 11

> Username: ldionne  
> Created at: 2015-06-13 13:40:52 UTC  
> Url: https://github.com/boostorg/hana/issues/86#issuecomment-111710903  

@viboes The note is right [there](http://ldionne.com/hana/group__group-functional.html).

---

## Comment 12

> Username: ldionne  
> Created at: 2015-08-09 06:55:26 UTC  
> Url: https://github.com/boostorg/hana/issues/86#issuecomment-129129016  

After thinking about this for a while, I'd rather leave Hana without dependencies and control the implementation of these function objects, which are used in a couple of implementation details of Hana.
