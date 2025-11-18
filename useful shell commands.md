## find command

`find . -name "split-examples.js"`
find the location of a file in the current directory, for example:
```sh
/app/dist # find . -name "split-examples.js"
./src/helpers/split-examples.js
```
 `grep -A3` -> display 3 lines of context after each match
### `xargs` for parallel commands
`echo {1..10} | xargs -n1 -P5 -I{} bash -c 'echo "Job {} started"; sleep 3; echo "Job {} finished"`

Delete pods parallely: `gp | awk 'NR > 1 && $1 ~ /worker-/ { print $1 }' | xargs -n1 -P5 kubectl delete pod`

