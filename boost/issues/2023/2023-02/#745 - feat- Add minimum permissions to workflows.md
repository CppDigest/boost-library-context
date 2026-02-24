# #745 - feat: Add minimum permissions to workflows [Closed]

> Username: gabibguti  
> Created at: 2023-02-24 17:26:25 UTC  
> Updated at: 2023-08-23 22:29:13 UTC  
> Closed at: 2023-08-23 22:29:12 UTC  
> Url: https://github.com/boostorg/boost/issues/745  

Adding minimum permissions to your workflows can help keep your repository safer against supply-chain attacks. I see your repo has 2 workflows, `ci.yml` and `release.yml`. `ci.yml` needs just `contents: read` permission and `release.yml` needs `contents: write` permissions for the jobs using `softprops/action-gh-release` action. If you agree I can open a PR making the changes.  
  
Restricting your workflow permissions is recommended by [GitHub itself](https://docs.github.com/en/actions/security-guides/automatic-token-authentication) and other security tools, such as [Scorecards](https://github.com/ossf/scorecard) and [StepSecurity](https://github.com/step-security).  
  
##### Additional context  
I'm Gabriela and I work on behalf of Google and the OpenSSF suggesting supply-chain security changes :)

---

## Comment 1

> Username: gabibguti  
> Created at: 2023-06-07 18:58:23 UTC  
> Url: https://github.com/boostorg/boost/issues/745#issuecomment-1581352013  

Hi! Friendly ping here. This issue has been idle for quite some time. Do you plan on considering these changes? If yes, please let me know! Otherwise I will wait up to 2 more months to close the issue. Thanks!

---

## Comment 2

> Username: gabibguti  
> Created at: 2023-08-23 22:29:12 UTC  
> Url: https://github.com/boostorg/boost/issues/745#issuecomment-1690727154  

Hey! Just to let you know I'm closing this issue. If you want to consider this change, please reopen :)
