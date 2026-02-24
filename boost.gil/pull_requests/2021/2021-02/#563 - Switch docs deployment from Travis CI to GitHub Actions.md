# #563 Switch docs deployment from Travis CI to GitHub Actions [Merged]

> Username: gopi487krishna  
> Created at: 2021-02-20 06:56:38 UTC  
> Updated at: 2021-03-06 10:51:56 UTC  
> Merged at: 2021-03-02 10:02:07 UTC  
> Closed at: 2021-03-02 10:02:08 UTC  
> Url: https://github.com/boostorg/gil/pull/563  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Migration of Documentation Deployment From Travis CI to GitHub Actions  
This pull request  is in reference to the issue #549   .    
## Changes Made  
  
-  A new workflow has been added named docs.yaml under the **.github/workflow** directory  
- Three new actions have been introduced    
- docs-prerequisites  : Gets all the necessary packages for generating the documentation   
- setup-boost : Sets up boost  
- generate-publish-doc : Generates the documentation and uploads the necessary files ( as per the branch ) to gh-pages branch  
  
<!-- What does this pull request do? -->  
  
### References  
#549   
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [ ] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: gopi487krishna  
> Created_at: 2021-02-20 06:58:01 UTC  
> Url: https://github.com/boostorg/gil/pull/563#issuecomment-782574933  

@mloskot @lpranam Sir kindly review the PR

---

## Comment 2

> Username: gopi487krishna  
> Created_at: 2021-02-20 07:02:18 UTC  
> Url: https://github.com/boostorg/gil/pull/563#issuecomment-782575631  

In travis configuration one thing I noticed is that the secure token was left exposed   
![image](https://user-images.githubusercontent.com/56198900/108586920-40c6b980-7377-11eb-8c3d-4e893b48b1eb.png)  
I don't know what is the reason for that but I think it would be better if GitHub secrets are used instead.   
  
Also currently as the secret is not created so the GitHub token field will be blank  
![image](https://user-images.githubusercontent.com/56198900/108586999-69e74a00-7377-11eb-95bb-3ca8087472b8.png)

---

## Review 3 [Changes requested]

> Username: lpranam  
> Created_at: 2021-02-20 12:04:25 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/563#pullrequestreview-594746121  

📁 .github/actions/generate-publish-doc/action.yml

```diff
  15 |+         chmod +x $GITHUB_WORKSPACE/.github/actions/generate-publish-doc/upload-script.sh
  16 |+         $GITHUB_WORKSPACE/.github/actions/generate-publish-doc/upload-script.sh ${{inputs.github_token}}
  17 |+       shell: bash
```

> Username: lpranam  
> Created_at: 2021-02-20 12:04:09 UTC  
> Updated_at: 2021-02-20 16:33:07 UTC  
> Url: https://github.com/boostorg/gil/pull/563#discussion_r579647932  

nitpicking: add new line at the end

> Username: gopi487krishna  
> Created_at: 2021-02-20 16:40:41 UTC  
> Url: https://github.com/boostorg/gil/pull/563#discussion_r579678717  

Resolved in commit 97a118b


📁 .github/workflows/docs.yaml

```diff
  15 |+       - uses: ./.github/actions/generate-publish-doc
  16 |+         with:
  17 |+           github_token: ${{ secrets.GIL_TEST_TOKEN }}
```

> Username: lpranam  
> Created_at: 2021-02-20 12:04:16 UTC  
> Updated_at: 2021-02-20 16:33:07 UTC  
> Url: https://github.com/boostorg/gil/pull/563#discussion_r579647946  

nitpicking: add new line at the end

> Username: gopi487krishna  
> Created_at: 2021-02-20 16:40:46 UTC  
> Url: https://github.com/boostorg/gil/pull/563#discussion_r579678722  

Resolved in commit 97a118b


---

## Comment 4

> Username: mloskot  
> Created_at: 2021-02-21 10:42:10 UTC  
> Url: https://github.com/boostorg/gil/pull/563#issuecomment-782836088  

@gopi487krishna   
> In travis configuration one thing I noticed is that the secure token was left exposed  
  
It is not exposed. It is encrypted.  
https://docs.travis-ci.com/user/environment-variables/#defining-encrypted-variables-in-travisyml

---

## Comment 5

> Username: gopi487krishna  
> Created_at: 2021-02-21 12:45:02 UTC  
> Updated_at: 2021-02-21 13:10:31 UTC  
> Url: https://github.com/boostorg/gil/pull/563#issuecomment-782852249  

> @gopi487krishna  
>   
> > In travis configuration one thing I noticed is that the secure token was left exposed  
>   
> It is not exposed. It is encrypted.  
> https://docs.travis-ci.com/user/environment-variables/#defining-encrypted-variables-in-travisyml  
  
@mloskot I am sorry for not looking at the docs prior to asking. Thanks for the help sir.

---

## Comment 6

> Username: mloskot  
> Created_at: 2021-03-01 20:40:25 UTC  
> Url: https://github.com/boostorg/gil/pull/563#issuecomment-788259556  

What's the status of this?

---

## Comment 7

> Username: gopi487krishna  
> Created_at: 2021-03-02 07:58:06 UTC  
> Updated_at: 2021-03-02 07:59:58 UTC  
> Url: https://github.com/boostorg/gil/pull/563#issuecomment-788700980  

@mloskot Its already completed.  Only the GIL_Token secret needs to be implemented at your end  
![image](https://user-images.githubusercontent.com/56198900/109616539-46808400-7b5b-11eb-8141-eb435664fde6.png)

---

## Comment 8

> Username: mloskot  
> Created_at: 2021-03-03 19:48:47 UTC  
> Url: https://github.com/boostorg/gil/pull/563#issuecomment-790009196  

@gopi487krishna I don;'t have much experience with the GitHub Actions yet, I wonder, does it have to be named `GIL_TEST_TOKEN`? Here, for example, in https://docs.github.com/en/actions/reference/authentication-in-a-workflow#using-the-github_token-in-a-workflow it says `secrets.GITHUB_TOKEN`.

---

## Comment 9

> Username: gopi487krishna  
> Created_at: 2021-03-05 15:53:47 UTC  
> Url: https://github.com/boostorg/gil/pull/563#issuecomment-791507364  

@mloskot  No sir, It can be named anything as per your wish. Here are the  steps just in case any confusion exists  
  
  
![image](https://user-images.githubusercontent.com/56198900/110135494-99289d00-7df4-11eb-9ef4-c018c95f228b.png)  
  
  
![image](https://user-images.githubusercontent.com/56198900/110135840-fde3f780-7df4-11eb-951e-7fe8b246dc4c.png)  
  
![image](https://user-images.githubusercontent.com/56198900/110135988-2b30a580-7df5-11eb-807b-f49b88fcf2cf.png)  
  
![image](https://user-images.githubusercontent.com/56198900/110137092-77301a00-7df6-11eb-8859-4d7969362afc.png)  
  
![image](https://user-images.githubusercontent.com/56198900/110137185-92028e80-7df6-11eb-8897-fc6ead2e9de3.png)  
  
![image](https://user-images.githubusercontent.com/56198900/110137398-d2faa300-7df6-11eb-939b-0ab266cb1ebe.png)  
  
![image](https://user-images.githubusercontent.com/56198900/110139243-d858ed00-7df8-11eb-9f88-297889e0c3c6.png)  
  
  
![image](https://user-images.githubusercontent.com/56198900/110137831-4a303700-7df7-11eb-9f20-50835d5ff959.png)

---

## Comment 10

> Username: gopi487krishna  
> Created_at: 2021-03-05 15:56:51 UTC  
> Updated_at: 2021-03-05 15:57:05 UTC  
> Url: https://github.com/boostorg/gil/pull/563#issuecomment-791509410  

@mloskot The secret name should be the  name here ( which you named earlier)  
![image](https://user-images.githubusercontent.com/56198900/110139816-751b8a80-7df9-11eb-8a03-41b9d51052b8.png)

---

## Comment 11

> Username: mloskot  
> Created_at: 2021-03-05 20:01:54 UTC  
> Updated_at: 2021-03-05 20:13:37 UTC  
> Url: https://github.com/boostorg/gil/pull/563#issuecomment-791649859  

@gopi487krishna Thanks for the very detailed guide.   
  
I've realised, interestingly, that I can not use `GITHUB_TOKEN` as a secret name:  
![image](https://user-images.githubusercontent.com/80741/110167275-d2add800-7df5-11eb-9e6f-f14957285f38.png)  
  
Apparently, it is a reserved name because, as per https://docs.github.com/en/actions/reference/authentication-in-a-workflow  
  
> GitHub automatically creates a GITHUB_TOKEN secret to use in your workflow.  
> You can use the GITHUB_TOKEN to authenticate in a workflow run.  
  
So, I'm trying it here https://github.com/boostorg/gil/commit/745d033ef2f382dea338851cfa6b2668f1e52905  
  
-----  
  
The GA job failed. I think, to publish generated content to `gh-pages` we will need to switch the manual `git push` in the upload script with dedicated action, eg. https://github.com/peaceiris/actions-gh-pages  
  
The `upload-script.sh` is removed, then the `./.github/actions/generate-publish-do` is updated to run these steps  
https://github.com/boostorg/gil/blob/745d033ef2f382dea338851cfa6b2668f1e52905/.github/actions/generate-publish-doc/upload-script.sh#L21-L25 but copying everything to `./public` directory from which the new docs are deployed with the action, as per the https://github.com/peaceiris/actions-gh-pages  
```  
- name: Deploy  
  uses: peaceiris/actions-gh-pages@v3  
  with:  
    github_token: ${{ secrets.GITHUB_TOKEN }}  
    publish_dir: ./public  
```

---

## Comment 12

> Username: gopi487krishna  
> Created_at: 2021-03-06 06:32:45 UTC  
> Url: https://github.com/boostorg/gil/pull/563#issuecomment-791883644  

@mloskot Sir is creating a PAT on your end not possible?

---

## Comment 13

> Username: mloskot  
> Created_at: 2021-03-06 06:35:30 UTC  
> Url: https://github.com/boostorg/gil/pull/563#issuecomment-791883923  

It's possible, but I'd prefer to ride on giants shoulder - if I can remove scripts replacing them with simple 3 lines, then it's my preference, less code to maintain the better. Don't worry I'll sort it out. Thanks

---

## Comment 14

> Username: gopi487krishna  
> Created_at: 2021-03-06 06:43:24 UTC  
> Updated_at: 2021-03-06 06:43:38 UTC  
> Url: https://github.com/boostorg/gil/pull/563#issuecomment-791884720  

@mloskot Yup you are right sir. I looked at one of the stack overflow posts to see if organizational access tokens can be created, as it turns out its not implemented for organizations at all. So a member of the organization used to typically create a PAT to do it . This as the first comment in the answer says is not a good way to do stuff and instead relying on GITHUB_TOKEN is the best thing  
  
[https://stackoverflow.com/questions/31159275/how-do-i-generate-the-github-oauth-token-for-organization-accounts](https://stackoverflow.com/questions/31159275/how-do-i-generate-the-github-oauth-token-for-organization-accounts)

---

## Comment 15

> Username: gopi487krishna  
> Created_at: 2021-03-06 06:54:15 UTC  
> Updated_at: 2021-03-06 06:54:33 UTC  
> Url: https://github.com/boostorg/gil/pull/563#issuecomment-791885646  

@mloskot I can do it by the evening if you want sir. I think its simple to do it. We have to only remove the staging,commiting and pushing part from the upload script and we can set the **publish_dir** directly to the temporary  directory and boom the thing works

---

## Comment 16

> Username: mloskot  
> Created_at: 2021-03-06 10:51:56 UTC  
> Url: https://github.com/boostorg/gil/pull/563#issuecomment-791911327  

@gopi487krishna Help and contributions are highly appreciated, so if you want to give it a go, please do.

---
