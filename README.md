# Scenario 1
Please write a simple CLI application in the language of your choice that does the following:

* Print the numbers from 1 to 10 in random order to the terminal.
* Please provide a README that explains in detail how to run the program on
MacOS and Linux.

<br></br>

>in `Linux` run the following commands:

```bash
seq 10 | shuf
```
<br></br>

>in `MacOS` run the following commands:
```shell
brew install coreutils
seq 10 | gshuf
```



<br></br>
<br></br>
# Scenario 2

Imagine a server with the following specs:
* 4 times Intel(R) Xeon(R) CPU E7-4830 v4 @ 2.00GHz
* 64GB of ram
* 2 TB HDD disk space
* 2 x 10Gbit/s nics

The server is used for SSL offloading and proxies around 25000 requests per second. Please
let us know which metrics are interesting to monitor in that specific case and how would you
do that? What are the challenges of monitoring this?

<br></br>

`[Answer is]:`

SSL performance is determined by a number of factors, including hardware, and the ciphers and certificates used. Performance can be broken down into three primary benchmark numbers:

* **Transactions Per Second:**  Primarily gated by the available CPU
* **Bulk throughput:**  Dependent upon CPU and the maximum packets per second
* **Concurrent connections:**  Memory is the limiting factor

<br>

> Increasing CPU cores allocated increases in SSL performance, as CPU is the primary constraint for both transactions per second and bulk throughput.


> Memory allocation, which primarily impacts the number of concurrent connections.

<br>

As a general rule of thumb, SSL connections consume about twice as much memory as HTTP layer 7 connections, and four times as much memory as layer 4 with TCP proxy.


**Memory consumption per connection:**
* 10 KB L4
* 20 KB L7
* 40 KB L7 + SSL



<br></be><br></be>

It is really important in this scenario should be clear some stuff like,
* does this server is bare metal or VM, if it's what kink of virtualization is using.
* what kink of backend server do we have for our SSL offloading.
* limit version of TLs support.
* security consideration, like the type of cipher support.
* OS model, and tunning of the OS



The amount of HTTP header buffering, caching, compression, and other features play a role in the final number.

<br></be><br></be>
**OS Tunning considerations:**
* inodes
* tcp accept Queue
* tcp sync Queue
* dynamic port range
* LimitNoFile
* nf conntrack
* use bounding for our NIC due to redundancy
* ....





<br></be><br></be>

| Type       | Description                                                                                       | Why?                                                                                                                           |
|------------|---------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| CPU Stats  | Time CPU spent                                                                                    | SSL Offloading is cpu sensitive process so its important to monitor CPU metrics (user,system,io,nice)                          |
| Disk Stats | Disk I/O metrics from /proc/diskstats                                                             | To measure DISK IO of the server while performing Encrytion / Decryption                                                       |
| FileSystem | Exposes filesystem statistics, such as disk space used.                                           | To track the disk space of the server from different partitions                                                                |
| loadavg    | System’s workload for last 15 minutes./proc/loadavg                                               | Load averages can be useful for a quick and dirty idea if a machine has gotten busier (for some definition of busier) recently |
| netstat    | Network statistics from /proc/net/netstat                                                         | Get key network metrics from SSL-offloading server                                                                             |
| meminfo    | Exposes the memory stat                                                                           | Memory related metrics of server to identify memory issues                                                                     |
| filefd     | Exposes metrics of file descriptor. Exposes file descriptor statistics from /proc/sys/fs/file-nr. | To check number of FD of the server                                                                                            |


<br></be><br></be>
---
 **How to monitor**:
 #
We can use open source tools to monitor the host with, `Exporter, Node Exporter(for the host), Grafana, Prometheus` or Netdata.



<br></br><br></br><br></br>
---
# Terminology and Tips

## SSL offloading
Nowadays, it is common (and convenient) to use the Load-Balancer SSL capabilities to cypher/uncypher traffic from clients to the web application platform.
<br></br>
Performing SSL at the Load-Balancer Layer is called **`SSL offloading`**, because you offload this process from your application servers.


## protocol scheme:
the client was using HTTPS while the connection on the application server is made over HTTP.

## destination port:
on the load-balancer, the port is usually 443 and on the application server should be 80 (this may change)

## L4
L4 load balancing offers traffic management of transactions at the network protocol layer (TCP/UDP). L4 load balancing delivers traffic with limited network information with a load balancing algorithm (i.e. round-robin) and by calculating the best server based on fewest connections and fastest server response times.

## L7
L7 load balancing works at the highest level of the OSI model. L7 bases its routing decisions on various characteristics of the HTTP/HTTPS header, the content of the message, the URL type, and information in cookies.





<br></br><br></br><br></br><br></br>
---
# References:
https://www.f5.com/services/resources/white-papers/understanding-adc-performance-metrics

https://avinetworks.com/docs/17.1/ssl-performance/

https://kuberneteslab.com/monitoring-proxy-server-traffic-stats-explained/
