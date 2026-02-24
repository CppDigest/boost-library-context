# #111 S390x Implementation: WIP [Merged]

> Username: Naveenaidu  
> Created at: 2019-07-31 13:27:29 UTC  
> Updated at: 2019-08-03 16:02:46 UTC  
> Merged at: 2019-08-03 06:25:58 UTC  
> Closed at: 2019-08-03 06:25:58 UTC  
> Url: https://github.com/boostorg/context/pull/111  

- [x] Implement architecture support  
- [x] Implement s390x_make_context  
- [x] Implement s390x_jump_context  
- [ ] Implement s390x_ontop_context  
- [ ] Test the asm files

---

## Comment 1

> Username: olk  
> Created_at: 2019-08-01 07:19:20 UTC  
> Updated_at: 2019-08-02 06:45:34 UTC  
> Url: https://github.com/boostorg/context/pull/111#issuecomment-517154941  

Did you run the unit-tests in sub-directory `test`?

---

## Review 2 [Changes requested]

> Username: olk  
> Created_at: 2019-08-02 06:45:17 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/context/pull/111#pullrequestreview-270008022  

`<binary_format>elf` in build/Jamfile.v2 should be `<binary-format>elf`

---

## Comment 3

> Username: Naveenaidu  
> Created_at: 2019-08-03 04:01:07 UTC  
> Url: https://github.com/boostorg/context/pull/111#issuecomment-517891991  

> `<binary_format>elf` in build/Jamfile.v2 should be `<binary-format>elf`  
  
Hey Oliver, Sorry for the late reply. I have updated the build file with the requested changes.  
  
> Did you run the unit-tests in sub-directory test?  
  
I'm sorry, but I don't know how to run the unit-tests from the sub-directory tests. It would be nice, if you can guide me through it, or even point me to a resource from where I can learn how to run it.

---

## Comment 4

> Username: olk  
> Created_at: 2019-08-03 06:25:53 UTC  
> Url: https://github.com/boostorg/context/pull/111#issuecomment-517899213  

enter directory `test` and execute `b2` - but the CI tests for s390x pass  
very nice - ty

---

## Comment 5

> Username: olk  
> Created_at: 2019-08-03 06:31:29 UTC  
> Url: https://github.com/boostorg/context/pull/111#issuecomment-517899470  

your pull request is missing the file `asm/ontop_s390x_sysv_elf_gas.S`

---

## Comment 6

> Username: Naveenaidu  
> Created_at: 2019-08-03 07:02:21 UTC  
> Url: https://github.com/boostorg/context/pull/111#issuecomment-517901149  

@olk Yeah, The `asm/ontop_s390x_sysv_elf_gas.S` is missing. I haven't implemented it yet. That's the reason I marked the PR as `WIP - Work in progress` :see_no_evil: .   
  
Sorry for that. I didn't know that I should open the PR only after I have all the files :see_no_evil: . I'll keep that in mind.  
  
> but the CI tests for s390x pass  
  
Does this mean that the implementation of `make` and `jump` work on s390x?

---

## Comment 7

> Username: olk  
> Created_at: 2019-08-03 07:14:22 UTC  
> Updated_at: 2019-08-03 10:05:10 UTC  
> Url: https://github.com/boostorg/context/pull/111#issuecomment-517901812  

> Does this mean that the implementation of make and jump work on s390x?  
  
I'm uncertain - better you apply ontop asm and run the unit tests

---

## Comment 8

> Username: olk  
> Created_at: 2019-08-03 15:44:04 UTC  
> Url: https://github.com/boostorg/context/pull/111#issuecomment-517934031  

- checkout github/boostorg/boost  
- build b2 tool  
- cd to libs/context/test  
- run unit tests by invoking b2 tool on command line

---

## Comment 9

> Username: olk  
> Created_at: 2019-08-03 16:02:45 UTC  
> Url: https://github.com/boostorg/context/pull/111#issuecomment-517935398  

Please create a pull request only if all files are available and unit tests pass.

---
