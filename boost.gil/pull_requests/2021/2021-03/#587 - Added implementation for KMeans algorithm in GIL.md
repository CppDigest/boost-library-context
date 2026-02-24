# #587 Added implementation for KMeans algorithm in GIL [Open]

> Username: Sayan-Chaudhuri  
> Created at: 2021-03-26 19:53:09 UTC  
> Updated at: 2022-06-03 06:52:40 UTC  
> Url: https://github.com/boostorg/gil/pull/587  

This is an implementation of Kmeans Clustering algorithm for Boost GIL. The input image filename(absolute/relative) must be passed as a command line argument.During execute ,input the required number of iterations and K value that indicates number of clusters.  
Output is stored in the file named output-kmeans.tif  
I have attached the example output of my implementation for the input image frog.jpg(attached),and number of clusters and iterations being 10 and 1 respectively.  
I would request the community to kindly provide feedback for my implementation  
I would also like to use the implementation of this algorithm for my gil competency test for GSOC 2021.  
  
<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
<!-- What does this pull request do? -->  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [ ] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: lpranam  
> Created_at: 2021-03-28 08:18:55 UTC  
> Url: https://github.com/boostorg/gil/pull/587#issuecomment-808864249  

This algorithm is used everywhere so shouldn't this be introduced as a usable functionality of the library instead of an example?

---

## Review 2 [Changes requested]

> Username: lpranam  
> Created_at: 2021-03-28 08:21:32 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/587#pullrequestreview-622730699  

we don't have to provide output file

---

## Comment 3

> Username: Sayan-Chaudhuri  
> Created_at: 2021-03-28 09:26:42 UTC  
> Url: https://github.com/boostorg/gil/pull/587#issuecomment-808870747  

I had thought that first my implementation will be checked whether its working correctly or not and so I did it in the form of an example. But if you suggest, I shall make it in the form of a usable functionality and then raise a pull request. I am also removing the output files following your suggestion.

---

## Comment 4

> Username: lpranam  
> Created_at: 2021-03-28 10:06:22 UTC  
> Url: https://github.com/boostorg/gil/pull/587#issuecomment-808874977  

First example and then the actual implementation just increases the amount of work reviewers and you will have to do. Let's just review the final thing.

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2021-03-28 20:47:42 UTC  
> Updated_at: 2021-04-01 07:57:45 UTC  
> Url: https://github.com/boostorg/gil/pull/587#issuecomment-808956954  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/587?src=pr&el=h1) Report  
> Merging [#587](https://codecov.io/gh/boostorg/gil/pull/587?src=pr&el=desc) (ca9e727) into [develop](https://codecov.io/gh/boostorg/gil/commit/6e91e4bf5c1bc9b5def5ef60e3e11bc475cbf11d?el=desc) (6e91e4b) will **increase** coverage by `0.13%`.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head ca9e727 differs from pull request most recent head e823ebd. Consider uploading reports for the commit e823ebd to get more accurate results  
```diff  
@@             Coverage Diff             @@  
##           develop     #587      +/-   ##  
===========================================  
+ Coverage    78.59%   78.72%   +0.13%       
===========================================  
  Files          117      118       +1       
  Lines         5003     5034      +31       
===========================================  
+ Hits          3932     3963      +31       
  Misses        1071     1071                
```

---

## Comment 6

> Username: Sayan-Chaudhuri  
> Created_at: 2021-03-29 09:18:22 UTC  
> Url: https://github.com/boostorg/gil/pull/587#issuecomment-809218283  

@lpranam  I have made the necessary changes as suggested .

---

## Comment 7

> Username: lpranam  
> Created_at: 2021-03-29 10:25:01 UTC  
> Url: https://github.com/boostorg/gil/pull/587#issuecomment-809266048  

new functionality also needs tests...

---

## Comment 8

> Username: Sayan-Chaudhuri  
> Created_at: 2021-03-29 20:03:02 UTC  
> Url: https://github.com/boostorg/gil/pull/587#issuecomment-809673732  

@lpranam Ok,I shall soon make the changes and update here

---

## Comment 9

> Username: Sayan-Chaudhuri  
> Created_at: 2021-03-30 10:49:28 UTC  
> Url: https://github.com/boostorg/gil/pull/587#issuecomment-810121330  

@lpranam  Is it ok if for the test file I keep a static dataset? I saw the test files of other algorithms where they have fixed the input and expected output data like the pixel value of images

---

## Comment 10

> Username: lpranam  
> Created_at: 2021-03-30 10:59:43 UTC  
> Url: https://github.com/boostorg/gil/pull/587#issuecomment-810127698  

@Sayan-Chaudhuri yes, as far as it covers the cases it is okay.

---

## Comment 11

> Username: Sayan-Chaudhuri  
> Created_at: 2021-03-31 18:08:10 UTC  
> Url: https://github.com/boostorg/gil/pull/587#issuecomment-811298505  

@lpranam I wish to upload the test file along with the KMeans implementation but I want to clarify certain things.  
1. I have used a  single dataset generated with the help of  make_blobs() function in python. I have tested it against the existing implementations of kmeans with random centre intialization like in sklearn and OPENCV. So, for benchmarking I have used the silhoutte score obtained using those implementations on that dataset.For a set of 20 runs of the algorithm using different centre initializations, I obtain a silhoutte score of  above 0.8 for 90% of the runs .Using the existing implementations in sklearn and OPENCV , this score has come to be 0.83 with the same number of runs. Is it OK If I submit my implementation then?  
2. I have implemented the entire silhoutte score algorithm while testing my implementation. Silhoutte score is very common in clustering techniques and Boost does not have a separate implementation for that. So should I make a separate header file for the Silhoutte score implementation so that it can be reviewed and later merged with the library if deemed ok? I have already googled and I found no api for Silhoutte score calculation in boost.  
  
If you can kindly find time to clarify these doubts, I will be highly grateful.

---

## Comment 12

> Username: Sayan-Chaudhuri  
> Created_at: 2021-03-31 19:14:33 UTC  
> Url: https://github.com/boostorg/gil/pull/587#issuecomment-811362220  

Upon your clarification, I shall push the files accordingly

---

## Comment 13

> Username: lpranam  
> Created_at: 2021-04-01 04:43:17 UTC  
> Updated_at: 2021-04-01 04:43:28 UTC  
> Url: https://github.com/boostorg/gil/pull/587#issuecomment-811633452  

you should upload it if, it does not fit then obviously can be removed but need to have look.

---

## Comment 14

> Username: lpranam  
> Created_at: 2021-04-01 04:43:59 UTC  
> Url: https://github.com/boostorg/gil/pull/587#issuecomment-811633657  

can also you PR must pass all the CI checks

---

## Comment 15

> Username: Sayan-Chaudhuri  
> Created_at: 2021-04-01 07:58:59 UTC  
> Url: https://github.com/boostorg/gil/pull/587#issuecomment-811725049  

Added a new header file for Kmeans

---

## Comment 16

> Username: Sayan-Chaudhuri  
> Created_at: 2021-04-01 07:59:10 UTC  
> Url: https://github.com/boostorg/gil/pull/587#issuecomment-811725156  

@lpranam

---

## Comment 17

> Username: Sayan-Chaudhuri  
> Created_at: 2021-04-01 08:01:50 UTC  
> Url: https://github.com/boostorg/gil/pull/587#issuecomment-811726824  

@lpranam I have also added the test file

---

## Comment 18

> Username: meshtag  
> Created_at: 2021-05-20 06:01:52 UTC  
> Url: https://github.com/boostorg/gil/pull/587#issuecomment-844726452  

Hi @Sayan-Chaudhuri ,  
I had a look at [this](https://github.com/boostorg/gil/pull/587/checks?check_run_id=2244613275) build which contains the following message  
  
> error: toolset gcc initialization:  
> error: version '8' requested but 'g++-8' not found and version '7.5.0' of default 'g++' does not match>   
  
I don't think this is related to any changes made by you in this PR, you should probably update this branch with latest develop branch of boost Gil.  
Pushing again after updating should probably solve this issue.  
  
PS : I encountered a similar error [here](https://github.com/boostorg/gil/pull/562#issuecomment-811979511)

---

## Comment 19

> Username: Sayan-Chaudhuri  
> Created_at: 2021-05-23 14:29:32 UTC  
> Url: https://github.com/boostorg/gil/pull/587#issuecomment-846572142  

@meshtag how to do so what you have mentioned?

---

## Comment 20

> Username: meshtag  
> Created_at: 2021-05-23 14:43:42 UTC  
> Url: https://github.com/boostorg/gil/pull/587#issuecomment-846574230  

There are a couple of ways to do this, you can look [here](https://stackoverflow.com/questions/3903817/pull-new-updates-from-original-github-repository-into-forked-github-repository) to understand some common ones.

---

## Comment 21

> Username: mloskot  
> Created_at: 2021-05-23 18:40:33 UTC  
> Updated_at: 2021-05-23 18:42:54 UTC  
> Url: https://github.com/boostorg/gil/pull/587#issuecomment-846606677  

@Sayan-Chaudhuri   
>how to do so what you have mentioned?  
  
Please, read the CONTRIBUTING.md on updating your PR.  
Updating/Syncing PR on GitHub is a common operation, so it is very well documented on the web, GitHub docs, StackOverflow.  
  
Not to mention the lazy button-based way recently offered by GitHub  
https://github.blog/changelog/2021-05-06-sync-an-out-of-date-branch-of-a-fork-from-the-web/

---
