# network-testing

Tools to assist an intrepid researcher in testing the performance of networks, wired or wireless.

# netburn
Netburn is a tool for testing network performance using an HTTP server back-end.

It will fetch an URL for a given number of seconds, but can limit itself to a given rate in Mbps. Rather than throttling the network connection itself, netburn simply monitors the number of bits received so far, compares it to the time elapsed (in microseconds), and refuses to fetch more pages until the rate so far is at or below the specified rate limit. After testing for the number of seconds requested, fetchrate returns information regarding throughput and latency of requests made.

~~~~
Usage:
         netburn -u {url} 
                [-r {rate limit} ] ... specified in Mbps (default none)
                [-t {seconds} ]    ... time to run test, default 30 
                [-o {filespec} ]   ... output filespec for CSV report 
                [--no-header ]     ... suppress header row of CSV output 
                [-h {hostname} ]   ... override system-defined hostname in CSV output
                [-q ]              ... quiet (suppress all but CSV output) 
                [--usage ]         ... you're looking at this right now 

Example:

         me@banshee:~/network-testing$ ./netburn -u http://remote.server/128K.bin -r 10

         Beginning test: fetching http://remote.server/128K.bin at maximum rate 10 Mbps for 30 seconds.

         Throughput so far: 10.01 Mbps
         Seconds remaining: 0

         Time elapsed: 30.2 seconds
         Number of pages fetched: 301
         Total data fetched: 37.6 MB
         Mean page length fetched: 1048576 KB
         Page length maximum deviation: 0 KB
         Throughput achieved: 10 Mbps
         Mean latency: 41.65 ms
         
         Worst latency: 190 ms
         99th percentile latency: 51 ms
         95th percentile latency: 46 ms
         90th percentile latency: 45 ms
         75th percentile latency: 43 ms
         Median latency: 41 ms
         Min latency: 35 ms
~~~~

# whenits
Whenits is a scheduler with millisecond-or-better precision.  Feed it a desired execution time in Unix epoch seconds, it will do a low-CPU-power sleep until 200ms before execution time, then a high-CPU-power loop to execute as close to the precise epoch time specified as possible.  

~~~~
Usage: 
         whenits {execution time in epoch seconds} {/path/to/command arg1 arg2 arg3...}

Example:
         me@banshee:~/network-testing$ ./whenits 1494534810 echo "hello world!"
         Sleeping 2.43815999031067 seconds.
         time is: 1494534810.003620
         hello world!

