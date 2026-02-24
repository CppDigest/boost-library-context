# #50 - name_generator changes questionable [Closed]

> Username: pdimov  
> Created at: 2017-11-04 16:29:03 UTC  
> Updated at: 2017-12-18 15:38:15 UTC  
> Closed at: 2017-11-04 23:50:10 UTC  
> Url: https://github.com/boostorg/uuid/issues/50  

I find your `name_generator` changes questionable. First, you've added an MD5 generator, but there should be no reason for it to ever be used. I don't see how this is an improvement. Second, you've "deprecated" `name_generator`, which makes no sense, as deprecation implies that a better alternative exists and should be used instead, and it doesn't.  
  
Using `name_generator` is best practice. Deprecation makes zero sense.

---

## Comment 1

> Username: Lastique  
> Created at: 2017-11-04 17:08:37 UTC  
> Url: https://github.com/boostorg/uuid/issues/50#issuecomment-341913370  

Some background is here: https://github.com/boostorg/uuid/issues/26.  
  
It is true that MD5 should not be used (it is provided only for completeness), but there's reason to believe that SHA1 will be deprecated as well one day in favor of a more secure hash function.

---

## Comment 2

> Username: pdimov  
> Created at: 2017-11-04 17:28:09 UTC  
> Url: https://github.com/boostorg/uuid/issues/50#issuecomment-341914981  

Let's elaborate then. What can code written _today_ use?  
  
1. `name_generator_md5`  
2. `name_generator_sha1`  
3. `name_generator`  
4. `basic_name_generator`  
  
1 is wrong. Code should never use MD5.  
2 is wrong, as it hardcodes the use of SHA1, which may be deprecated in the future.  
3 is correct. It will automatically switch to the new alternative when it's available.  
4 is either 1, 2, or a violation of the spec.  
  
So, we've managed to deprecate the only correct choice, and we've added a few incorrect choices. This makes client code worse.  
  
It's `name_generator_md5` and `name_generator_sha1` that should be "deprecated", not `name_generator`. Except of course those didn't exist before, so deprecation makes no sense for them either.

---

## Comment 3

> Username: Lastique  
> Created at: 2017-11-04 17:40:32 UTC  
> Url: https://github.com/boostorg/uuid/issues/50#issuecomment-341915790  

Makes sense. Unless someone wants to have a predictable conversion from textual names to UUIDs. `name_generator` is probably good as the default, which is suitable for general use. More specific names are available for when they are needed. `basic_name_generator` is just a building block.

---

## Comment 4

> Username: jeking3  
> Created at: 2017-11-04 17:52:00 UTC  
> Updated at: 2017-11-04 18:45:39 UTC  
> Url: https://github.com/boostorg/uuid/issues/50#issuecomment-341916550  

The MD5 generator was added to complete the implementation relative to the RFC.  I agree it is unlikely to be used.  It is also not loaded into a compilation unit unless explicitly asked for by name.  
  
The problem comes when SHA1 is deprecated in favor of something else.  If someone is using `name_generator` and then the next version of boost changes the definition of `name_generator` to use a different hashing function, anything hashed before with `name_generator` is invalidated and this could break any logic based on data hashed by a previous version of boost.  
  
This is why I split them out, and specifically want folks to switch from using `name_generator` to `name_generator_sha1` so that when `name_generator_whatever_is_next` comes out, their code won't break.  And when that whatever comes next is out, `name_generator` will be removed for safety.

---

## Comment 5

> Username: pdimov  
> Created at: 2017-11-04 18:01:13 UTC  
> Url: https://github.com/boostorg/uuid/issues/50#issuecomment-341917289  

I agree that people who require predictable UUID generation would want to tie themselves to SHA1 for eternity. People who do not, will not, and having their code change automatically to the better alternative will be a benefit to them, not a downside.  
  
Yes, using hardcoded SHA1 will not "break", but it will also not fix itself when a better alternative becomes available. So it's not clear what specific safety does not using `name_generator` provide. It forces you to make your code perpetually unsafe.

---

## Comment 6

> Username: jeking3  
> Created at: 2017-11-04 18:10:24 UTC  
> Url: https://github.com/boostorg/uuid/issues/50#issuecomment-341918077  

The assumption is that if one is using a `name_generator` they need it to be stable across time.  Therefore they should start with deciding which hash algorithm they want to use up front.  
  
What we could do is add `name_generator_latest`, if someone wants to automatically track changes to the current hashing algorithm, but I need them to do it consciously, which is why I want `name_generator` to retire because it would not be safe to change the definition to anything other than sha1 as it could break all existing consumers.

---

## Comment 7

> Username: pdimov  
> Created at: 2017-11-04 18:23:38 UTC  
> Url: https://github.com/boostorg/uuid/issues/50#issuecomment-341919080  

Adding `name_generator_latest` seems a good compromise. With it, all clients will have a non-deprecated option to which they can switch depending on their use case.
