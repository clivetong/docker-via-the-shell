- What is there inside a docker container?

  - run docker hello world
    docker run hello-world

  - export and untar

  ```
    docker save hello-world:latest -o foo.tar
    tar tvf foo.tar
  ```

  - look at the contents

- [Namespaces](https://www.youtube.com/watch?v=0kJPa-1FuoI)

  - quick introduction to the 7 namespaces
  - /proc/\$\$/ns/ and readlink

  ```
    ls /proc/$$/ns
    readlink /proc/$$/ns/uts

  ```

* lightweight virtualization

* now we go namespace by namespace

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

  - build a docker container that sleeps

  - run and enter using nsenter and show the UTS nmespace effect

  - mount

  - proc

  - io

  - pid

  - user

  - cgroup

* Now we have what we need
  - Set up the namespace to isolate the hello-world application
  - run hello world
