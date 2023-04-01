

# Behaviours of the PS command between osx (Ventura) and Fedora



## ps (no flag)

I tested on Fedora; the basic `ps` command (no flag) only shows process status associated with the *current*  terminal session. In **osx Ventura**, the result include all processes associated with all tty terminals.  For example, if I have one iterm2 terminal running in, the `ps` command shows:

```
$ ps
  PID TTY           TIME CMD
68923 ttys000    0:00.26 -bash
```

Now,  if I open another terminal, the ps command contains `ttys000` and `ttys001` :

```
$ ps
  PID TTY           TIME CMD
68923 ttys000    0:00.26 -bash
69284 ttys001    0:00.22 -bash
```

On osx the `ps` command follows BSD conventions instead of POSIX conventions. To have `ps` command by itself (no flag) behave similar to that in Fedora (shows only the processes associated to current terminal) you can use the `-T` flag ([ref](https://apple.stackexchange.com/questions/300864/how-to-get-the-basic-linux-ps-functionality-in-mac)):

```
$ ps -T
  PID TTY           TIME CMD
68922 ttys000    0:00.03 /usr/bin/login -fpl john /Users/john/Applications/iTerm.app/Contents/MacOS/ShellLauncher --launch_shell
68923 ttys000    0:00.26 -bash
69723 ttys000    0:00.01 ps -T
```

Note that in the above the `TTY` column only contains `ttys000` (no `ttys001`).

The man page for `ps` on osx said:

```
-T      Display information about processes attached to the device associated
        with the standard input.
```

Which I assume the device mentioned is the terminal.

---

### ps x

According to the Fedora man page, the `x` flag in Fedora (without `-`) will show processes **owned by `you`** (not limit to that associated to the *current*  terminal session):

```
       x      Lift the BSD-style "must have a tty" restriction, which is imposed
              upon the set of all processes when some BSD-style (without "-") options
              are used or when the ps personality setting is BSD-like.  The
              set of processes selected in this manner is in addition to the
              set of processes selected by other means.  An alternate description
              is that this option causes ps to list all processes **owned by you** (same
              EUID as ps), or to list all processes when used together with the a option.
```

The Fedora's man page did not mention anything about using `x` flag with dash, but `-x` works. It appears that with or without dash the result are the same (the number line of processes returns are the identical):

```
$ ps -x | wc -l
78
$ ps x | wc -l
78
```

Also, the column generated by both `ps -x` or `ps x` in **Fidora** are the same as well:

```
$ ps -x | head -n 1
    PID TTY      STAT   TIME COMMAND
$ ps x | head -n 1
    PID TTY      STAT   TIME COMMAND
```

In **osx Ventura**, the results return different number of process:

```
$ ps -x | wc -l
     547

$ ps x | wc -l
     546
```

The column generated also different:

```
$ ps -x | head -n 1
  PID TTY           TIME CMD

$ ps x | head -n 1
  PID   TT  STAT      TIME COMMAND
```

The **osx Ventura** man page mention the `x` flag with a dash:

```
  -x      When displaying processes matched by other options, include
          processes which do not have a controlling terminal.  This is
          the opposite of the -X option.  If both -X and -x are specified
          in the same command, then ps will use the one which was specified last.
```

Note that the above does not say anything about *owning by you*. It says **include process which do not have a controlling terminal**.

#### Conclusion

In Fedora use `x`without dash:

```
$ ps x
```

In osx use `x` with dash:

```
$ ps -x
```



## Todo

[ps ef or ps -ef?](https://askubuntu.com/questions/942529/which-to-use-of-ps-ef-or-ps-ef)

[ps -aux or ps -aux](https://askubuntu.com/questions/942529/which-to-use-of-ps-ef-or-ps-ef)

[ps aux](https://stackoverflow.com/questions/7138783/how-to-display-the-current-process-tree-of-a-bash-session)

[ps -ef](https://unix.stackexchange.com/questions/62182/please-explain-this-output-of-ps-ef-command)

[osx ps show hierarchy](https://www.google.com/search?q=osx+ps+show+hierarchy&oq=osx+ps+show+hierarchy&aqs=chrome..69i57j33i10i160.9140j0j7&sourceid=chrome&ie=UTF-8)

[process in tree](https://stackoverflow.com/questions/7138783/how-to-display-the-current-process-tree-of-a-bash-session)

[UNIX-, BSD-, GNU-options in Linux's ps command. Where are they from?](https://unix.stackexchange.com/questions/78691/unix-bsd-gnu-options-in-linuxs-ps-command-where-are-they-from)
[What is the difference between standard syntax and BSD syntax?](https://askubuntu.com/questions/484982/what-is-the-difference-between-standard-syntax-and-bsd-syntax)

[https://www.linode.com/docs/guides/use-the-ps-aux-command-in-linux/](https://www.linode.com/docs/guides/use-the-ps-aux-command-in-linux/)

[COMMAND_MODE=legacy](https://www.oradba.ch/2011/04/mac-os-x-terminal-compatibility-settings/)