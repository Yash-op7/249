## meaning of `$#`
In **shell scripting**, **`$#`** has a very specific meaning:

> **It represents the number of positional parameters (arguments) passed to the script or function.**

example:
```sh
#!/bin/bash

echo "Number of arguments: $#"
```
Run it like this: `bash args.sh apple banana cherry`
Output: `Number of arguments: 3`
