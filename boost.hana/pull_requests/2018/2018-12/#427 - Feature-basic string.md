# #427 Feature/basic string [Open]

> Username: yamasdais  
> Created at: 2018-12-02 08:00:03 UTC  
> Updated at: 2019-03-12 13:52:14 UTC  
> Url: https://github.com/boostorg/hana/pull/427  

Hello,  
  
I wanted to use hana::wstring but couldn't find. So, I tried to make it.  
Could you please check it out?  
  
- Added hana::basic_string<CharType>  
- Added hana::wstring as hana::basic_string<wchar_t>  
- Added tests for hana::basic_string<> and hana::wstring.  
  
EDIT: 2019-03-12  
I have tried various work around to pass the Travis test for clang 3.5, but it can't.  
`/home/travis/build/yamasdais/hana/include/boost/hana/basic_string.hpp:116:20: error: no matching function for call to 'prepare_impl'`  
`            return prepare_impl<CastChar<CharTo, CharFrom>>(`  
`/home/travis/build/yamasdais/hana/include/boost/hana/basic_string.hpp:108:9: note: candidate template ignored: substitution failure [with Cast = boost::hana::basic_string_detail::CastChar<wchar_t, wchar_t>, S = tmp, N = <0, 1, 2, 3>]: non-type template argument is not a constant expression`  
`        prepare_impl(S, std::integer_sequence<std::size_t, N...>)`  
  
If anyone has a good idea, please help me.  
  
  
Regards,

---

## Comment 1

> Username: ricejasonf  
> Created_at: 2019-02-27 17:43:21 UTC  
> Url: https://github.com/boostorg/hana/pull/427#issuecomment-467959941  

Hi. It looks like you need to rebase onto the develop branch. Also commits should be squashed into a single commit (Item 5 in https://github.com/boostorg/hana/blob/master/CONTRIBUTING.md)

---

## Comment 2

> Username: yamasdais  
> Created_at: 2019-02-28 12:17:53 UTC  
> Url: https://github.com/boostorg/hana/pull/427#issuecomment-468251959  

Hi. Thank you for the comment.  
I'll give it a try

---

## Comment 3

> Username: yamasdais  
> Created_at: 2019-03-02 12:15:03 UTC  
> Url: https://github.com/boostorg/hana/pull/427#issuecomment-468915263  

I renewed the branch from the 'develop branch' a single commit.  
  
Regards,

---

## Comment 4

> Username: ricejasonf  
> Created_at: 2019-03-02 18:17:23 UTC  
> Updated_at: 2019-03-02 18:23:32 UTC  
> Url: https://github.com/boostorg/hana/pull/427#issuecomment-468945910  

Try this:  
  
1. Make a new branch off of the latest `develop` @ 45369732ea74e136f2e979c6380c322779726f95  
2. Add your commit  
    - `git cherry-pick 70038edbee2fe877caf051352d6b92e75279d7d3`  
3. Force push to your original branch on the remote   
    - `git push --force <remote> <new branch>:feature/basic_string`  
  
I did not try step 3 myself, but it should update this PR to have only your commit.  
  
EDIT: FWIW I don't know how much of an issue the weird commits are, and I am not the one who will accept the PR anyways.

---

## Comment 5

> Username: yamasdais  
> Created_at: 2019-03-02 23:16:48 UTC  
> Url: https://github.com/boostorg/hana/pull/427#issuecomment-468969731  

I learned git operations I have never used these few days :smiley:  
  
`$ git checkout -b feature/basic_string_pr1 upstream/develop`  
`$ git log --oneline | head -1`  
45369732e [Travis] Fix build with Clang 7  
`$ git cherry-pick 70038edbee2fe877caf051352d6b92e75279d7d3`  
`$ git push --force origin feature/basic_string_pr1:feature/basic_string`  
  
It seems a `clean' commit you might expected.  
Thank you for the advice, anyway.

---
