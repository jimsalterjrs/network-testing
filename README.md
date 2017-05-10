# network-testing

Tools to assist an intrepid researcher in testing the performance of networks, wired or wireless.

# fetchrate
Fetchrate is an alternative to ApacheBench, which grabs pages rapidly from an HTTP server.  ApacheBench won't allow you to throttle your request rate.  Fetchrate will.  Returns time, throughput, and latency statistics.

Usage: fetchrate \[url] \[rate limit in Mbps] \[seconds to run test]

# whenits
Whenits is a scheduler with millisecond-or-better precision.  Feed it a desired execution time in Unix epoch seconds, it will do a low-CPU-power sleep until 200ms before execution time, then a high-CPU-power loop to execute as close to the precise epoch time specified as possible.  

Usage: whenits \[execution time in epoch seconds] \[/path/to/command arg1 arg2 arg3...]
