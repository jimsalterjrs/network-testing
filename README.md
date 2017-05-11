# network-testing

Tools to assist an intrepid researcher in testing the performance of networks, wired or wireless.

# netburn
Netburn is a tool for testing network performance using an HTTP server back-end.

It will fetch an URL for a given number of seconds, but can limit itself to a given rate in Mbps. Rather than throttling the network connection itself, netburn simply monitors the number of bits received so far, compares it to the time elapsed (in microseconds), and refuses to fetch more pages until the rate so far is at or below the specified rate limit. After testing for the number of seconds requested, fetchrate returns information regarding throughput and latency of requests made.

Usage: netburn -u {url} 
               [-r {rate limit} ] ... specified in Mbps (default none)
               [-t {seconds} ]    ... time to run test, default 30 
               [-o {filespec} ]   ... output filespec for CSV report 
               [--no-header ]     ... suppress header row of CSV output 
               [-q ]              ... quiet (suppress all but CSV output) 
               [--usage ]         ... you're looking at this right now 

# whenits
Whenits is a scheduler with millisecond-or-better precision.  Feed it a desired execution time in Unix epoch seconds, it will do a low-CPU-power sleep until 200ms before execution time, then a high-CPU-power loop to execute as close to the precise epoch time specified as possible.  

Usage: whenits \[execution time in epoch seconds] \[/path/to/command arg1 arg2 arg3...]
