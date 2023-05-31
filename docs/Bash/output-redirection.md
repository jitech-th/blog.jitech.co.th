# Redirect stdout and stderr to File

- stdin: 0
- stdout: 1
- stderr: 2

## Usage

- #### Standard redirection (stdout)

```
$ cat sample.txt > output.txt
# or
$ cat sample.txt 1> output.txt
```

- #### Redirecting stderr and stdout to separate files

```
$ cat sample.txt > output.txt 2> error.txt
```

- #### Redirecting stderr to stdout

```
$ cat sample.txt > output.txt 2>&1
```

- #### Redirecting stdout and stderr to a single file

```
$ cat sample.txt &> output.txt
```

reference: [Redirect stdout and stderr to File](https://linuxhint.com/redirect-stdout-and-stderr-to-file/)