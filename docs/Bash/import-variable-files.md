# Import variable files in Bash

## Instructions

- #### variable file `vars`
```
# vars
var1="abcd"
var2="1234"
```

- #### import it to our bash script
```
#!/bin/bash

source vars # <--- import the vars file

echo "${var1}${var2}
```

- #### output
```
$ chmod +x our-script
$ ./our-script
abcd1234
```