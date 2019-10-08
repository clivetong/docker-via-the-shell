- What is there inside a docker container?

  - run docker hello world
  - export and untar
  - look at the contents

- [Namespaces](https://www.youtube.com/watch?v=0kJPa-1FuoI)

  - quick introduction to the 7 namespaces
  - /proc/\$\$/ns/ and readlink
  - nsenter (targeting a process)
  - ushare
  - lightweight virtualization

- now we go namespace by namespace

  - UTS
  - walk through the UTS namespace in two bash windows showing their independence
  - build a docker container that sleeps
  - run and enter using nsenter and show the UTS nmespace effect

  - mount

  - proc

  - io

- Now we have what we need
  - Set up the namespace to isolate the hello-world application
  - run hello world
