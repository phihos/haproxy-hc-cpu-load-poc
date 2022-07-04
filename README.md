# haproxy-hc-cpu-load-poc

This is a PoC showing that a backend server with a L4 health check causes high CPU load when down compared to a backend server with no health check.
We can reproduce that by starting two Vagrant VMs, install HaProxy, block the backend server IP via iptables and deploy a nearly identical config.
The only difference is the `check` parameter on the server.

## Quickstart

Install Vagrant and VirtualBox and run
```bash
vagrant up
```

Afterwards you have the high CPU load example on port 8080 and the low CPU load example on port 8081. 
You can find the status page on both ports on `/stats`.

Next log into the machines and start htop to monitor CPU load:

``` bash
vagrant ssh haproxy-high-cpu
sudo htop
```

``` bash
vagrant ssh haproxy-low-cpu
sudo htop
```

Next you can start to put some load on the example instances. In this case we use the tool `wrk` but any load testing tool should be fine.

```bash
watch wrk http://<host-ip>:8080 -c 100 -t 4 -d 120
```

```bash
watch wrk http://<host-ip>:8081 -c 100 -t 4 -d 120
```

It is recommended to do this from another machine since wrk uses a lot of CPU itself.

Now you should be able to see that the high CPU load example indeed uses a lot more CPU than the other one. The only difference is the `check` parameter on the backend's server.
You can find the config files at the root of this repo.
