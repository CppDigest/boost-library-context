# #45 - wait_group reference [Closed]

> Username: anarthal  
> Created at: 2023-07-24 11:00:00 UTC  
> Updated at: 2023-07-24 11:12:38 UTC  
> Closed at: 2023-07-24 11:12:37 UTC  
> Url: https://github.com/boostorg/cobalt/issues/45  

https://klemens.dev/async/#wait_group  
  
* Having it be a `struct` looks weird  
* `__wait_one_op__`, `__wait_op__` look like unrendered conent  
* `// wait for one tasks to complete` syntax error, should be "wait for one task"  
* `// swallow the exception here, and wait for the completion` I don't understand what does this function do  
* `// wait for one tasks to complete` This is missing some details - It waits for a task to complete and removes it from the group once it's completed. The returned awaitable holds a reference to the `wait_group` internal data, so it has view semantics. That should be documented for every function with such semantics.  
* `std::size_t size() const;` Specify here that completed tasks count towards the wait group size until they are removed, either via one of the wait functions, or explicitly via `reap()`.  
* What's the behavior if a task fails or is cancelled? Does `wait_one` / `wait_all` remove it? I've got the feeling this is related to the constructor parameter, but it's not evident.
