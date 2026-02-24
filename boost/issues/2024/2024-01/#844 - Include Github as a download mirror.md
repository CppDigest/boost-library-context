# #844 - Include Github as a download mirror [Open]

> Username: Chocobo1  
> Created at: 2024-01-01 07:01:14 UTC  
> Updated at: 2025-01-01 13:45:44 UTC  
> Url: https://github.com/boostorg/boost/issues/844  

Currently there are only 2 download site for boost. One is jfrog which is still [unavailable](https://github.com/boostorg/boost/issues/842) at the time of current writing. Another one is sourceforge which is slow and I had faced reliability issues in the past.  
I propose to utilize Github as another (backup) download site.  
  
Boost is already putting release artifacts on github: https://github.com/boostorg/boost/releases  
The script for creating those artifacts is here: https://github.com/boostorg/boost/blob/master/.github/workflows/release.yml  
However those artifacts on github are not the same as from jfrog or sourceforge, they have different file tree layout and different hashes. Please consider fixing them or upload the official ones.  
  
ps. nowadays Github Actions has non trivial amount of CI jobs running (for various projects) and those CI jobs would benefit (in download speed and reliability) if boost can be fetched over the same github domain.

---

## Comment 1

> Username: mclow  
> Created at: 2024-01-01 18:27:45 UTC  
> Url: https://github.com/boostorg/boost/issues/844#issuecomment-1873429633  

The Boost libraries are incredibly popular, and are downloaded 1000s of times each day.  
Before we put up any other mirrors, we need to discuss it with the providers to (at the very least) let them know what we are doing.  
  
If you know someone who is willing to donate several 100 TB of bandwidth/month, we'd love to talk to them.  
  
Hopefully, the JFrog links will be restored soon.

---

## Comment 2

> Username: valiko-ua  
> Created at: 2024-01-02 17:22:52 UTC  
> Updated at: 2024-01-02 17:23:39 UTC  
> Url: https://github.com/boostorg/boost/issues/844#issuecomment-1874318979  

As an alternative, consider splitting archives:  
* sources only  
* documentation only  
* tests only  
* built binaries for specific platform (this seems already done)  
  
Most users need just the first one. This will greatly decrease required bandwidth.

---

## Comment 3

> Username: userdocs  
> Created at: 2024-01-05 14:52:15 UTC  
> Updated at: 2024-01-05 14:53:53 UTC  
> Url: https://github.com/boostorg/boost/issues/844#issuecomment-1878800304  

First request of 2024 to use Github releases for boost. 🥳  
  
At any point will you [actually ask Github](https://github.com/boostorg/boost/issues/502) instead of using this copy and paste [reply](https://github.com/boostorg/boost/issues/844#issuecomment-1873429633)  
  
This is such a weird flex to take that somehow your release assets will cripple Github and you are so afraid of some perceived repercussion you never actually resolve the issue.   
  
>[!CAUTION]  
> They probably won't even notice this repo exists even with the release assets for INTERNAL traffic to other repos using the assets in actions. You act like they will put you in jail.  
  
Either:  
  
- ASK THEM, which will probably just amount to a discussions topic with no commitment from them.  
- or just do it and see what happens.  
  
Really, what is the worst that will happen? They ask you to stop using releases? At least you'll then have a valid reason.  
  
After the numerous previous attempts at a reasonable outcome to this subject some things occur to me:  
  
1: I still have no idea why you think that amount of transfer is a lot, especially to Github. Really, it's not.  
2: The majority of traffic will be internal network traffic used in workflows, This is not the burden you make it out to be.  
3: They probably won't bite if you ask or care if you do it without asking them.  
4: There are much bigger projects on Github doing it, why are you special?  
5: I can just host it myself on Github and move on.  
  
And that is exactly what I have been doing for some time as externally hosted assets are unreliable for workflows and using Github to track my dependencies is far mor reliable.  
  
https://github.com/userdocs/qbt-workflow-files/releases/latest  
  
I just came here to lol at the reply. It's just silly at this point.

---

## Comment 4

> Username: userdocs  
> Created at: 2024-01-07 01:30:03 UTC  
> Updated at: 2024-01-07 10:25:57 UTC  
> Url: https://github.com/boostorg/boost/issues/844#issuecomment-1879906546  

First, part of the OP's issue was never addressed so i'll link to me asking it (why the release assets are different)   
 https://github.com/boostorg/boost/issues/753#issuecomment-1472602242  
  
Second, I made an automated mirror of the https://archives.boost.io/release/ assets as it really is that simple.  
  
https://github.com/userdocs/boost/releases/latest   
  
Third: How many times does boost need to have cdn drama for this it not be an issue?

---

## Comment 5

> Username: Chocobo1  
> Created at: 2024-01-07 09:21:53 UTC  
> Updated at: 2024-01-07 10:24:54 UTC  
> Url: https://github.com/boostorg/boost/issues/844#issuecomment-1880002865  

JFrog is down [again](https://github.com/boostorg/boost/issues/843#issuecomment-1879898835)...  
  
Here is the relevant documentation from Github about bandwidth usage:  
>[Storage and bandwidth quotas](https://docs.github.com/en/repositories/releasing-projects-on-github/about-releases#storage-and-bandwidth-quotas)  
Each file included in a release must be under 2 GiB. There is no limit on the total size of a release, nor bandwidth usage.  
  
I would take it literally unless they say it isn't.  
  
>Before we put up any other mirrors, we need to discuss it with the providers to (at the very least) let them know what we are doing.  
  
Sure, it is a nice thing to do. But at the same time I'm not sure there is actually interest in Boost to take this step.  
Anyway, here is the form to submit a support request: https://support.github.com/contact?tags=rr-general-technical

---

## Comment 6

> Username: edmcman  
> Created at: 2024-01-07 12:58:39 UTC  
> Url: https://github.com/boostorg/boost/issues/844#issuecomment-1880053198  

Or use jsdelivr?

---

## Comment 7

> Username: userdocs  
> Created at: 2024-01-07 23:06:47 UTC  
> Updated at: 2024-01-07 23:06:59 UTC  
> Url: https://github.com/boostorg/boost/issues/844#issuecomment-1880208329  

> Or use jsdelivr?  
  
As a recommendation for a backup mirror perhaps but the point that should be focused on here is that there is no valid to reason to avoid using Github as the main release platform and mirror that out to backup cdn solutions. The reason(s) constantly given as to why it is not/has not been done are simply out of touch, wrong and misleading. I thought they were dumb 2 years ago and now I think it's a meme.  
  
Github's own TOS, platform documentation and comparable projects directly support the idea it can be done and contradict claims that boost is some unreasonable burden on the platform.  
  
It's also not really a good reflection on the project to be constantly mired by this cdn drama when they could just use Github because that is what the majority of people need.  To use boost in their Github Workflows.   
  
The only sane outcome here is that the proposal is accepted and road mapped so no one ever has to wonder if their boost workflow is a coinflip.

---

## Comment 8

> Username: userdocs  
> Created at: 2024-01-11 17:16:19 UTC  
> Url: https://github.com/boostorg/boost/issues/844#issuecomment-1887610341  

As my final contribution to this spectacle I thought I'd expand the potential of the demo mirror repo to be an easy to fork and reproduce mirror system that can either just host source archives or both source and binary archives, depending on what's needed. I saw 2 people download the binary files so figured I had to account for them.  
  
https://github.com/userdocs/boost  
  
Though it's a proof of concept mirror repo it is fully functional, automated and will probably just work to mirror future releases as they are released so I'll just leave it there and see what happens.  
  
Regardless, I don't have to worry about future cdn issues, when it happens again.

---

## Comment 9

> Username: dimpase  
> Created at: 2024-03-19 23:40:02 UTC  
> Url: https://github.com/boostorg/boost/issues/844#issuecomment-2008349795  

I've never heard of any project afraid of mirroring small, by modern standards, tarballs on GitHub.

---

## Comment 10

> Username: trim21  
> Created at: 2025-01-01 13:44:12 UTC  
> Updated at: 2025-01-01 13:45:44 UTC  
> Url: https://github.com/boostorg/boost/issues/844#issuecomment-2567015559  

> Or use jsdelivr?  
  
jsdelivr have a hard limit at 50mib and it only serve files in npm or github repo.
