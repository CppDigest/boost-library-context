# #542 Remove duplicate runs from GHA [Merged]

> Username: mborland  
> Created at: 2021-02-20 07:10:21 UTC  
> Updated at: 2021-02-20 11:45:22 UTC  
> Merged at: 2021-02-20 10:17:22 UTC  
> Closed at: 2021-02-20 10:17:23 UTC  
> Url: https://github.com/boostorg/math/pull/542  

GHA currently runs twice for a commit to a branch that is also a pull request [such as this.](https://github.com/boostorg/math/pull/541) This change will narrow down the runs to just PRs and a push to master or develop. Also adds CI runs to releases since there is one upcoming. Update readme now that GHA [natively supports [ci skip].](https://github.blog/changelog/2021-02-08-github-actions-skip-pull-request-and-push-workflows-with-skip-ci/)

---
