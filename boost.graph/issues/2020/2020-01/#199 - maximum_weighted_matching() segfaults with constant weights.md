# #199 - maximum_weighted_matching() segfaults with constant weights [Closed]

> Username: count0  
> Created at: 2020-01-20 16:13:22 UTC  
> Updated at: 2025-02-20 09:54:02 UTC  
> Closed at: 2025-02-10 02:26:54 UTC  
> Url: https://github.com/boostorg/graph/issues/199  

Some graphs cause maximum_weighted_matching() to segfault due to a stack overflow when unit weights are used.  
  
Please see minimal example linked below which is a simple modification of the standard example from the library.  
   
https://gist.github.com/count0/551867e10973d4b2dc047b8528908281

---

## Comment 1

> Username: yi-ji  
> Created at: 2020-01-25 03:46:48 UTC  
> Url: https://github.com/boostorg/graph/issues/199#issuecomment-578373607  

Thank you for the bug report, I've confirmed it. I will look into it for a fix (might take me some time).  
Meanwhile this might be is helpful: the O(n^4) implementation in an earlier [commit](https://github.com/boostorg/graph/commit/400eed8f02a6f03612f06c77a754a5fb78e24bec#diff-4a9c7f6ea04f56263cd8175c5e880ad9) seems to be fine, regarding this problem.

---

## Comment 2

> Username: count0  
> Created at: 2020-01-28 23:49:36 UTC  
> Url: https://github.com/boostorg/graph/issues/199#issuecomment-579527970  

@yi-ji Thanks for looking into it.  
  
However, when trying the O(n^4) implementation from the commit you mentioned, I also get a segfault for the same example...

---

## Comment 3

> Username: count0  
> Created at: 2022-01-02 14:13:39 UTC  
> Url: https://github.com/boostorg/graph/issues/199#issuecomment-1003722406  

@yi-ji is there any update on this? Or at least a previous version that is known to work?

---

## Comment 4

> Username: jeremy-murphy  
> Created at: 2022-01-03 23:15:26 UTC  
> Url: https://github.com/boostorg/graph/issues/199#issuecomment-1004419928  

If you have a minimal unit test that fails, I would be happy to merge that even without a fix.  
  
Btw, if you two are regular users of BGL, we could do with help in terms of reviewing pull requests.  
I don't have time to deeply check every pull request, and I don't actually expect other people to either, but at least if three people all agree that something looks good then we can merge it with some confidence.   
And if it turns out to be bad, then no single person is to blame.  ;)

---

## Comment 5

> Username: count0  
> Created at: 2022-01-04 08:23:43 UTC  
> Url: https://github.com/boostorg/graph/issues/199#issuecomment-1004609090  

@jeremy-murphy  The minimal unit test is here: https://gist.github.com/count0/551867e10973d4b2dc047b8528908281  
  
I'd be happy to help reviewing pull requests.

---

## Comment 6

> Username: cmdawson  
> Created at: 2022-01-04 11:52:25 UTC  
> Url: https://github.com/boostorg/graph/issues/199#issuecomment-1004744510  

Also happy to review pull requests.  
  
On Tue, Jan 4, 2022 at 6:23 PM Tiago de Paula Peixoto <  
***@***.***> wrote:  
  
> @jeremy-murphy <https://github.com/jeremy-murphy> The minimal unit test  
> is here: https://gist.github.com/count0/551867e10973d4b2dc047b8528908281  
>  
> I'd be happy to help reviewing pull requests.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/graph/issues/199#issuecomment-1004609090>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABABHWJIIN7XDDQIUO77ZTTUUKVBTANCNFSM4KJGVBMQ>  
> .  
> Triage notifications on the go with GitHub Mobile for iOS  
> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>  
> or Android  
> <https://play.google.com/store/apps/details?id=com.github.android&referrer=utm_campaign%3Dnotification-email%26utm_medium%3Demail%26utm_source%3Dgithub>.  
>  
> You are receiving this because you are subscribed to this thread.Message  
> ID: ***@***.***>  
>

---

## Comment 7

> Username: jeremy-murphy  
> Created at: 2022-01-05 02:55:05 UTC  
> Url: https://github.com/boostorg/graph/issues/199#issuecomment-1005338420  

Great. Can you all email me and we can start organizing.

---

## Comment 8

> Username: jeremy-murphy  
> Created at: 2022-07-12 06:18:28 UTC  
> Url: https://github.com/boostorg/graph/issues/199#issuecomment-1181360551  

I just realized, after all this time, that maybe you can't see my email address on my GitHub profile.  :)  
That's OK, now that I've rediscovered this thread and reminded myself who you are, I'll tag you on other issues of importance. I think this is not the only issue related to `maximum_weighted_matching`.

---

## Comment 9

> Username: SneakyPeaky  
> Created at: 2023-03-31 15:55:13 UTC  
> Updated at: 2023-03-31 15:55:49 UTC  
> Url: https://github.com/boostorg/graph/issues/199#issuecomment-1492184005  

Can't boost just take any implementation from https://uoj.ac/problem/81 (see https://uoj.ac/submission/559649) ?  
I am using the fastest implementation from there because boost fails with segfaults on random tests.

---

## Comment 10

> Username: jeremy-murphy  
> Created at: 2023-12-04 23:22:02 UTC  
> Url: https://github.com/boostorg/graph/issues/199#issuecomment-1839709092  

> Can't boost just take any implementation from https://uoj.ac/problem/81 (see https://uoj.ac/submission/559649) ? I am using the fastest implementation from there because boost fails with segfaults on random tests.  
  
I can't merge it if you haven't made a pull request for it.

---

## Comment 11

> Username: SneakyPeaky  
> Created at: 2023-12-05 10:15:51 UTC  
> Url: https://github.com/boostorg/graph/issues/199#issuecomment-1840446618  

> > Can't boost just take any implementation from https://uoj.ac/problem/81 (see https://uoj.ac/submission/559649) ? I am using the fastest implementation from there because boost fails with segfaults on random tests.  
>   
> I can't merge it if you haven't made a pull request for it.  
  
Oh, no, I mean why don't @yi-ji just base his implementation on the one from in UOJ? Just scratch the whole   
 maximum_weighted_matching and replace it with code in link I sent. It's clearly much better.

---

## Comment 12

> Username: jeremy-murphy  
> Created at: 2025-02-15 10:15:59 UTC  
> Url: https://github.com/boostorg/graph/issues/199#issuecomment-2660855199  

I'm interested in hearing confirmation from @count0 that this bug is fixed, and how it benchmarks against UOJ from @SneakyPeaky .

---

## Comment 13

> Username: count0  
> Created at: 2025-02-15 13:05:52 UTC  
> Url: https://github.com/boostorg/graph/issues/199#issuecomment-2660919124  

I can confirm that I no longer observe the segfault with the new code!

---

## Comment 14

> Username: SneakyPeaky  
> Created at: 2025-02-15 13:13:29 UTC  
> Url: https://github.com/boostorg/graph/issues/199#issuecomment-2660921663  

@jeremy-murphy , don't wanna be a buzzkill here, but the new code will never beat the one recommended [in this comment](https://github.com/boostorg/graph/issues/199#issuecomment-1492184005). The one used in UOJ's best submission is [Blossom V algorithm by Vladimir Kolmogorov](https://pub.ista.ac.at/~vnk/papers/blossom5.pdf). That's why I am surprised boost opted for some other obscure implementation, instead of simply copy pasting the one already available from UOJ.  
But regardless, if the segfault is gone, that works for me as well. Good job.

---

## Comment 15

> Username: jeremy-murphy  
> Created at: 2025-02-15 23:43:33 UTC  
> Url: https://github.com/boostorg/graph/issues/199#issuecomment-2661149735  

It's no buzz kill, don't worry, because this change was entirely focused on fixing bugs and any performance gain was a bonus.  
It's good to know just how far from optimal the performance is. Numbers are what matter.

---

## Comment 16

> Username: jeremy-murphy  
> Created at: 2025-02-20 09:54:00 UTC  
> Url: https://github.com/boostorg/graph/issues/199#issuecomment-2670997230  

I want to add that even if another algorithm has a better asymptotic efficiency, there could well be, in fact likely are, certain kinds or sizes of problems that the less efficient algorithm will still be faster at.   
That's why std::sort is not pure quicksort, it's why Dantzig's simplex method is still popular.   
No offence to anyone, but we have to eschew claims that amount to, "This algorithm is the greatest.  You've never seen a faster algorithm. This algorithm could end poverty."  
We have to be scientific, and that means data, peer review, etc.  
Sorry, couldn't help wanting to make the comparison in the current political climate. :)
