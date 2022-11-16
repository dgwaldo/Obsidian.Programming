## Bisect (used to find the last good commit)

```
git checkout KNOWN_BAD_COMMIT
git bisect start
git bisect bad
git bisect good KNOWN_GOOD_COMMIT
```
Look at which commits have been marked good or bad

```
git bisect log
```

Stop the session
```
git bisect reset 
```