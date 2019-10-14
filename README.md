- What is there inside a docker container?

  - run docker hello world
    docker run hello-world

  - export and untar

  Note the export is gives us everything we need to run the container, including the various layers
  and details about the entry point when the container runs.

  ```
    docker save hello-world:latest -o foo.tar
    tar tvf foo.tar
  ```

  - look at the contents (and notice that this has no layers)
    The hello-world is actually a binary that doesn't need any of the user space linux libraries

  - build something that does need some Linux user space
    Write a Dockerfile that uses bash to print something to the output

  - notice that now there are layers in the Docker messages

  - take that apart and find the tarred layers inside the existing tar

- [Namespaces](https://www.youtube.com/watch?v=0kJPa-1FuoI)

  - quick introduction to the 7 namespaces

  - /proc/\$\$/ns/ and readlink

  ```
    ls /proc/$$/ns
    readlink /proc/$$/ns/uts
  ```

  - show that inside the kernel each process (task) has a proxy for the various namespaces

    - The kernel handles a proxy for the namespaces: https://github.com/torvalds/linux/blob/master/kernel/nsproxy.c#L29
    - The main namespace copy mechanism: https://github.com/torvalds/linux/blob/master/kernel/nsproxy.c#L60
    - They are switched into the task struct: https://github.com/torvalds/linux/blob/master/kernel/nsproxy.c#L213
    - For example the setns syscall: https://github.com/torvalds/linux/blob/master/kernel/nsproxy.c#L233
    - The namespace proxy is associated with the task: https://github.com/torvalds/linux/blob/a2953204b576ea3ba4afd07b917811d50fc49778/include/linux/sched.h#L910

  now we go namespace by namespace to try to show how this all really works

  - UTS
  - walk through the UTS namespace in two bash windows showing their independence

    - unshare to set up a new namespace

```
 sudo unshare -u bash
 hostname bananaman
 hostname
```

    - nsenter to enter a namespace (but can target a process)

```
  ps # in the new namespace process
  sudo nsenter -a -t ...pid...
```

- proc

Freeze processes and move cross machine (so need to impersonate PIDs)

Process 1 is special in Linux world (init) and hardwired into the OS as the zombie waiter

Do this inside and outside a container

```
ps aux
```

- network

Own ARP and Routing information

```
ip netns add red
ip netns add blue
ip netns
```

Network interfaces

```
ip link
```

Run inside the namespace

```
ip netns exec red ip link
ip -n red link
```

Do the same with `route` and `arp`

Use a virtual cable

```
ip link add veth-red type veth peer name veth-blue
ip link set veth-red netns red
ip link set veth-blue netns blue
```

And assign ips

```
ip -n red addr add 192.168.15.1 dev veth-red
ip -n blue addr add 192.168.15.2 dev veth-blue
```

and bring them up

```
ip -n red link set veth-red up
ip -n blue link set veth-blue up
```

and now ping works

```
ip netns exec red ping 192.168.15.2
```

and see it has identified it's neighbour

```
ip netns exec red arp
```

Lookign on the host we see nothing

```
arp
```

To kill one end (And hence the other end too)

```
ip -n red link del veth-red
```

Add a BRIDGE here

- mount

Overlay

```
mkdir overlay-play
cd overlay-play
mkdir lower
mkdir upper
mkdir working
mkdir union
sudo mount -t overlay -o lowerdir=lower,upperdir=upper,workdir=working none union
cat > lower/play1
^D
ls union
cat > union/play3
^D
ls -l union
ls -l upper
```

- io

* Now we have what we need
  - Set up the namespace to isolate the hello-world application
  - run hello world
