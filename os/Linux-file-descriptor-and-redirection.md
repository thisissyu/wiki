# [study-linux] Linux file descriptor and redirection

Create a new file descriptor

```bash
touch temp.txt
# Create a new file descriptor 3
#  The created file descriptor only available in current shell session, associated with process number.
exec 3> temp.txt
# Check this descriptor, method 1
# `$$` means get the current session's process number.
sudo ls -l /proc/$$/fd/3
# Check this descriptor, method 2
sudo lsof -p $$ | grep ' 3w '
# Then, you can use this new file descriptor to redirect stdin, stdout and stderr as normal.
# Usually, the self-created descriptor's name between 3 and 9.
```

Temp file descriptor

```bash
# Save `ls` command's output to a temp file descriptor and echo this descriptor's path:
❯ echo <(ls)
/proc/self/fd/11

# For example:
# Save `ls` command's output to a temp file descriptor and return this descriptor by `<(ls)`,
# and use this descriptor as the `tee` command's stdin by `<`,
# then stdout the content to `temp.txt` by `tee` command
tee temp.txt < <(ls)
# This is like:
ls | tee temp.txt
```

Case

```bash
# This below is error
# That's beacuse the `>` is created before sudo, and sudo only works on `ls`, not works on `>`,
# and due to we don't have the enough permission for `/etc` folder when creating `>`, so it complains error.
sudo ls > /etc/temp.txt  # Output error

# These below is correct
sudo ls | sudo tee /etc/temp.txt
# Or
sudo sh -c 'ls > /etc/temp.txt'
```
