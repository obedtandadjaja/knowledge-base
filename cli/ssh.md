# SSH

## Tunnel

There are two ways to create an SSH tunnel, local and remote port forwarding.

### Local port forwarding

Use case: You are on a private network which doesn't allow connections to a specific server. To get around this we can create a tunnel through a server which isn't on our network and thus can access that server.

```
ssh -L 9000:server-host-name:80 <host>
```

`-L` indicates local forwarding.

In here we say that we are forwarding our local port `9000` to `server-host-name:80`. The `<host>` should be replaced with the name of your computer.

