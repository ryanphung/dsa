# Intuition

* The inclusive range `[l, r]` should include the target if it exists
* `m = ~~((l + r) / 2)`
* When moving down: `r = m - 1`. But sometimes we do `r = m` (if we want to still include `m` in the range)
* When moving up: `l = m + 1`. We cannot do `l = m` because it may lead to infinite loop (for example `l = 2, r = 3, m = 2` which leads to `l = 2` again).
* For simplicity, in the discussion and in the sample code, we compare `m` with the index of the target (called `target`). In reality we can't do a direct comparison like that, and it's usually in the form of a function (for example `nums[m] === value`)

## Standard algorithm (exact target):
* If `m === target` then `return m`
* If `m < target` then move up: `l = m + 1`
* If `m > target` then move down: `r = r - 1`
* Ends loop when l === m but we still want to do a last comparison of `m` against `target`, therefore we use `while (l <= r)`.
* If the loop ends, it means that we have checked `l === m === r` but `m` is not the `target`.

```
let l = 0, r = n - 1, m;

while (l < r) {
  m = ~~((l + r) / 2);

  if (m === target) return m;
  else if (m < target) l = m + 1;
  else r = m;
};

return -1; // not found
```

## Lower bound algorithm
* If `m < target` then move up: `l = l + 1`
* If `m > target` then move down: `r = r - 1`
* If `m === target` then move down (`r = m` since we still want to make sure the range [l, r] includes the target)
* To simplify: If `m >= target` then move down with `r = m`.
* Ends loop when `l === r`. We don't need to do a final comparison of `m` against `target`, therefore just use `while (l < r)`.

```
let l = 0, r = n - 1, m;

while (l < r) {
  m = ~~((l + r) / 2);

  if (m < target) l = m + 1;
  else if (m > target) r = m - 1; // alternatively, remove this line
  else r = m;
}

return l === target ? l : -1; // not found
```

## Upper bound algorithm
* If `m < target` then move up: `l = m + 1`
* If `m > target` then move down: `r = m`. Note that we use `r = m` here, which means we deliberately wants `r` to be `> target`, and the target should be in the range of `[0, r - 1]`
* If `m === target` then move up (we have to use `l = m + 1` to avoid infinite loop as explained above. Combine with the above, this means that the range `[l - 1, r - 1]` actually includes the target now).
* Therefore, to simplify: If `m <= target` then move up with `l = m + 1`.
* Ends loop when `l === r`, therefore `while (l < r)`.
* Once the loop is ended, we look for the target at `l - 1`
* Another thing to note is that, `r` should start at `n` rather than `n - 1`.
* *Question* Why don't we let `l` start at `1` too? Actually, `l` should start at `1`.

```
let l = 1, h = n, m;

while (l < r) {
  m = ~~((l + r) / 2);

  if (m <= target) l = m + 1;
  else r = m;
}

return l > 0 && l - 1 === target ? l : -1; // not found
```

* *Question* Then why don't we shift `l` and `h` to the left by 1?

```
let l = 0, h = n - 1, m;

while (l < r) {
  m = ~~((l + r) / 2);

  if (m + 1 <= target) l = m + 1;
  else r = m;
}

return l === target ? l : -1; // not found
```