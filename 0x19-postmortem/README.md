# 0x19 - Postmortem

## Issue Summary

The web-01 server was down for six hours between 00:00 and 06:00.

All services on the web server were inaccessible but the load balancer was still sending half of the user traffic to the server affecting about 50% of users that requested our services during the downtime.

The main cause of the issue is a memory overload caused by the server's memory and cache not being cleared regularly. In addition to that, the implementation of the new datadog agent pushed the server beyond its capacity resulting in a crash.

## Timeline - Nov 4

**23:00** – A junior engineer was attempting to implement the new datadog agent on the server.

**00:00** - After finishing up the setup process, the engineer noticed that server response time was slowing down and attempted a reboot. After the reboot, attempts to ssh into the server were failing.

**00:30** – According to the datadog site report, the server was still online and the engineer was working under the assumption that it was a local problem. 

**00:30** - Tried logging in from a different device. Attempted ssh after deleting the saved known hosts.

**01:00** - Reached out for help to peers and mentors on Slack.

**03:00** – A peer suggested that they had been in a similar situation and that the situation resolved itself in about 24 hours.

**05:00** - Received suggestion to destroy virtual machine of server and start over with the project by mentor.

**06:00** - After reinitiating a new web-01 server, setup was complete and the services were back online.

## Root cause and resolution

The issue was caused because of an unexpected memory overload on the server in question. Adding another service on the machine without addressing the initial memory issues caused it to crash and made it unrecoverable. After reaching out to peers and mentors, a final verdict was made to destroy the virtual machine and reinitiate a new server to solve the issue. Soon afterwards, the load balancer was redirecting user traffic to the newly set up web-01 server.

## Corrective and Preventative measures

### Reduce time needed to create new server

 * Use bash or puppet scripts to automate generating new servers in case any should fail

 * Set up a system to report if a server goes down and is unrecoverable to immediately replace it.

### Prevent server crashes

 * Automated cache and memory clearance on server

 * Increase usable server memory

 * Scheduled server update and restart

### Managing user traffic in case of crash

 * Automated monitoring of server availability by load balancer

 * Balancing load of requests based on availability
