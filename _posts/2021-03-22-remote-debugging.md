---
layout: post
title:  "Remote Debugging"
date:   2021-03-22 07:30:00 -0500
permalink: /remote-debugging/
categories: programming
---
## Use Case
Suppose you have a production instance that is running java and you want to debug a request flow.

## Thinking process
There are two ways of doing it - either add debug statements(and keep restarting) or perform remote debugging.

## How to perform remote debugging?
1. On the remote server
    1. Open /etc/init.d/your-java-app and add DEBUG1=" -Xdebug -Xrunjdwp:transport=dt_socket,address=33114,server=y,suspend=n".
    2. Start it - sudo service your-java-app restart
2. On your local
    1. Create a tunnel between your local and the prod instance using "ssh 10.0.0.111 -L 33114:localhost:33114"
    2. Now open Intellij and add a "Remote JVM Debug" configuration with 33114 port. Select appropriate JDK Version.
    3. Start it.
    4. Add some breakpoints to the request flow.
    5. Hit the remote server with a request - http://10.0.0.111:3000/endpoint?pid=781
    6. See that the breakpoint is being hit.
    
Thanks for reading along.
