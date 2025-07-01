# wsl2 problems

## the ip exposure

When I run a wsl vm with a uid: user1, the vm has an ip address. Everyone else in the host can ping this ip address.

If user1 runs a web service, all other users can access this website.

```shell
# get ip in the wsl vm
ip a

# ping the ip via the terminal of another user
```

## Security issue

Is there a security issue when we activate CPU virtualization in the host?

```shell
Set-VMProcessor -VMName "WIN22" -ExposeVirtualizationExtensions $true
```

## Performance issue

When the wsl is executed via runas, there are performance issues. Need to verify in our environment.
```shell

```