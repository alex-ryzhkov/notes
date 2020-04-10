## Set up Vagrant network bridge

Using vagrant we can configure the vm to bridge directly to your LAN. This way any device on the LAN can access the running VM.

1. First find the VirtualBox bridges and names.
```
VBoxManage list bridgedifs | grep ^Name
```
2. In the vagrant file find the line where the current network is configured. It should start / look something like
```
config.vm.network :private_network, ip: findip()
```
3. Replace that line using your bridged connection:
```
config.vm.network :public_network, :bridge => "your_network_device_name"
```
4. run
```
vagrant reload
```

[Source](https://github.com/daftlabs/creed/wiki/Set-up-Vagrant-network-bridge)

