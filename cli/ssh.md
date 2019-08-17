# SSH

## Tunnel

There are two ways to create an SSH tunnel, local and remote port forwarding.

### Local port forwarding

Lets you connect from your local computer to another server. 

```
ssh -L 9000:server-host-name:80 <host>
```

`-L` indicates local forwarding.

In here we say that we are forwarding our local port `9000` to `server-host-name:80`. The `<host>` should be replaced with the name of your computer.

Note that port numbers less than `1024` or greater than `49151` are reserved for the system.

The destination server can even be the same as the SSH server:

```
ssh -L 5900:localhost:5901 <host>
```

### Remote port forwarding

Lets you connect from the remote SSH server to your local computer (or another server).

```
ssh -R 5900:localhost:5900 guest@remote-pc
```

`-R` indicates remote port forwarding.

For the duration of the SSH session, gues would be able to access your `5900` port from his computer.

### Dynamic port forwarding

Turns your SSH client into a SOCKS proxy server. SOCKS is a little-known but widely-implemented protocol for programs to request any Internet connection through a proxy server.

```
ssh -C -D 1080 <host>
```

`-D` option specifies dynamic port forwarding. `1080` is the standard SOCKS port but you can use other port numbers. `-C` enables compression, which speeds up the tunnel when proxying text-based information, but can slow down when proxying binary information.

### Forwarding GUI Programs

SSH can also forward graphical applications over a network. Although it can take some work and extra software to forward programs to Windows or Mac OSX.

#### Single applications

Note that to do this for Mac OSX, you will need to install and start the X11 server before using ssh.

```
ssh -X <host>
```

`-X` option specifies forwarding X11 connections.

Once connection is made, type the name of your GUI program on SSH cli:

```
firefox &
```

Your program should start as normal, although you might find it's a little slower than it would be. The trailing `&` means that the program should run in the background mode, so you can start typing new commands straight away.
