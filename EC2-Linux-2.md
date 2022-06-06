## Part 2 - Searching Files

- Display techproed.txt file in reverse, use `tac` command

```bash
tac techproed.txt
```

- Create reverse-techproed.txt in reverse of techproed.txt

```bash
tac techproed.txt > reverse-techproed.txt
```


### `find` command

- Find all the files whose name is techproed.txt in a current working directory.

```bash
find . -name techproed.txt
```

- Find all the files under /home directory with name techproed.txt

```bash
find /home -name techproed.txt
```

- Find all the files whose name is techproed.txt and contains both capital and small letters in /home directory.

```bash
find /home -iname techproed.txt
```

- Find all directories whose name is new folder in /home directory, use `-type d` option

```bash
find /home -type d -name new
```

- Find all txt files in the working directory, use `-type f` option

```bash
find . -type f -name "*.txt"
```

- Find all empty files in the working directory, use `-empty` option

```bash
find . -type f -empty
```

- Find all empty files in the /home directory, specify the path

```bash
find /home -type f -empty
```

- To find all 100MB files under home directory, use `-size` option

```bash
find /home -size 100M
```

- Find all the files which are greater than 50MB and less than 100MB under home directory. 
- Note that the `+` and `-` prefixes signify greater than and less than.

```bash
find /home -size +50M -size -100M
```

- Find all the files which are modified 1 day ago in /home directory, use `-mtime` option

```bash
find /home -mtime 1
```

- **Find all the files which are modified in the last 10 days in /home directory, use `-` option**

```bash
find /home -mtime -10
```

- Find all the files which are modified in more than 10 days in /home directory,, use `+` option

```bash
find /home -mtime +10
```

- Find all the files which are modified more than 10 minutes back and less than 30 minutes in **current folder**
- Use `-mmin` option

```bash
find . -mmin +10 -mmin -30
```

- Find all the files which are modified more than 10 minutes back and less than 30 minutes in **new** or any other directory you choose.
- Use `-mmin` option

```bash
find . -mmin +1 -mmin -30
```

### `grep` command

Grep is a Linux / Unix command-line tool used to search for a string of characters in a specified file. 

- Create a file and name it `grep.txt`.

```txt
grep  searches  for  PATTERNS  in  each  FILE.
PATTERNS  is  one  or more patterns separated by newline characters, and grep prints each line that matches a pattern.  
Typically PATTERNS should be  quoted  when grep is used in a shell command.
```

- Create another file and name it `linux.txt`

```txt
Linux is a family of open-source Unix-like operating systems based on the Linux kernel.
It is an operating system kernel first released on September 17, 1991, by Linus Torvalds.Linux is typically packaged in a Linux distribution.
Distributions include the Linux kernel and supporting system software and libraries.
Popular Linux distributions include Debian, Fedora, and Ubuntu. 
Commercial distributions inlude Red Hat Enterprise Linux and SUSE Linux Enterprise Server.
```

- Search `linux.txt` file for `kernel`. 

```bash
grep "kernel" linux.txt
```

- Search all files for `is`

```bash
grep  "is" *
```

- Search `linux.txt` file for `linux`

```bash
grep "linux" linux.txt
```

- It didn't find linux expression. Because **grep is case sensitive.** Now try with the following command.

```bash
grep -i "linux" linux.txt
```

- Search `linux.txt` file for `ker`.

```bash
grep -i "ker" linux.txt
```

- Now search `linux.txt` file for `ker` with the following command.

```bash
grep -w "ker" linux.txt
```

- It didn't find `ker`. Grep allows you to find and print the results for whole words only with `-w` flag. Let's try with the following command.

```bash
grep -w "kernel" linux.txt
```

- We can display the lines that are not matched with `-v` that means "invert the match" in grep, in other words, **return all non matching lines**

```bash
grep -v "kernel" linux.txt
```

- The `^` regular expression pattern specifies the start of a line. This can be used in grep to match the lines which start with the given string or pattern

```bash
grep "^Li" linux.txt
```

- The $ regular expression pattern specifies the end of a line. This can be used in grep to match the lines which end with the given string or pattern.

```bash
grep "kernel.$" linux.txt
```
