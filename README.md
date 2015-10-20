# docker-netperf
A docker image for netperf testing.

If the container is run without arguments, it will start a netserver daemon. The container also has the netperf client installed, so you can start another container to run the client.

## Example

To compare throughput between Docker host networking and bridged networking:

```
$ # Run host-network netperf test
$ docker run -dt --net=host --name netserver-host paultiplady/netperf
$ docker run -it --net=host paultiplady/netperf netperf –l 10 -i 10 -I 95,1 -c -j -H 127.0.0.1 -t OMNI -- -D  -T tcp -O THROUGHPUT,THROUGHPUT_UNITS,STDDEV_LATENCY,LOCAL_CPU_UTIL
OMNI Send TEST from 0.0.0.0 (0.0.0.0) port 0 AF_INET to 127.0.0.1 () port 0 AF_INET : +/-0.500% @ 95% conf.  : nodelay
Throughput Throughput  Stddev       Local  
           Units       Latency      CPU    
                       Microseconds Util   
                                    %      
25220.39   10^6bits/s  23.82        30.31  


$ # Run bridged-network netperf test
$ docker run -dt --name netserver-bridge paultiplady/netperf
$ ip=$(docker inspect --format '{{ .NetworkSettings.IPAddress }}' netserver-bridge)
$ docker run -it paultiplady/netperf netperf –l 10 -i 10 -I 95,1 -c -j -H $ip -t OMNI -- -D  -T tcp -O THROUGHPUT,THROUGHPUT_UNITS,STDDEV_LATENCY,LOCAL_CPU_UTIL
OMNI Send TEST from 0.0.0.0 (0.0.0.0) port 0 AF_INET to 172.17.0.74 () port 0 AF_INET : +/-0.500% @ 95% conf.  : nodelay
Throughput Throughput  Stddev       Local  
           Units       Latency      CPU    
                       Microseconds Util   
                                    %      
11198.09   10^6bits/s  32.46        31.51  

