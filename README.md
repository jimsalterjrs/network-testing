<p align="center"><img src="http://openoid.net/openoid-white-on-red-274x51.png" width=274 height=51> &nbsp;&nbsp;&nbsp;&nbsp; <img src="http://openoid.net/gplv3-127x51.png" width=127 height=51></p>

# netburn
Netburn is a tool for testing network performance using an HTTP server back-end. It's similar to ApacheBench, but where ApacheBench is intended primarily to test the HTTP server itself, netburn is intended primarily to test the network between them.

It will fetch an URL for a given number of seconds, but can limit itself to a given rate in Mbps. Rather than throttling the network connection itself, netburn simply monitors the number of bits received so far, compares it to the time elapsed (in microseconds), and refuses to fetch more pages until the rate so far is at or below the specified rate limit. After testing for the number of seconds requested, netburn returns information regarding throughput and latency of requests made.

**IMPORTANT NOTE** about the -c {concurrency} option: if you ask for -c 10, each "page" will consist of 10 parallel fetches of URL, and the "latency" will be the amount of time it takes to get the last bit from the last concurrent child fetch. Another new "page" fetch will not begin until after ALL child fetches complete - this simulates the experience of browsing web pages with multiple resources, and is very different from Apachebench's -c 10, which merely hammers the server as hard as possible from ten completely independent client processes!

~~~~
Usage:
         netburn -u {url} 
                [-r {rate limit} ]  ... specified in Mbps (default none)
                [-t {seconds} ]     ... time to run test, default 30 
                [-o {filespec} ]    ... output filespec for CSV report 
                [--no-header ]      ... suppress header row of CSV output 
                [-h {hostname} ]    ... override system-defined hostname in CSV output
                [-c {concurrency} ] ... number of concurrent URL fetches to make per 'page' fetch
                [-q ]               ... quiet (suppress all but CSV output) 
                [-ifinfo {iface} ]  ... output detailed wifi info about {iface}
                [--usage ]          ... you're looking at this right now 

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

# net-hydra
`net-hydra` is a multiple client controller which executes a given set of commands on multiple remote machines with an extremely high degree of simultaneity.  Although net-hydra was specifically designed to be used with netburn to simultaneously test a network using multiple devices, it can be used for scheduling remote execution of *any* arbitrary command or commands.  However, it does require `whenits` to be present on the client machines.

Since `net-hydra` uses SSH command channels, it's usable even without shared passwordless keys on the client machines - you can enter in passwords or key passphrases at runtime, and it'll use the created command channel to schedule the jobs without needing the password/passphrase again.

It's highly recommended to use `ntpd` to keep accurate time on the server and all clients. `net-hydra` will refuse to schedule jobs if all clients do not have system time accurate to within 1 second of the server's.

~~~~
Usage: 
	net-hydra {configfile}

	net-hydra requires a config file specifying [clients], [directives], and optionally
	[aliases], which it uses to schedule jobs to run simultaneously in the very near future
	on multiple client machines.

		# sample nethydra.conf
		[clients]
			# list each client device here, by name and as SSH will connect to them.
			#
			local0 = root@127.0.0.1
			local1 = root@127.0.0.2
	
		[directives]
			# These are the command(s) to be run on each client device. Directives
			# beginning with $ reference lines from the [aliases] section. Any use
			# of $when, either here or in [aliases], will be replaced with the
			# scheduled execution time, in Unix epoch seconds.
			#
			local0 = $4kstream
			local1 = /usr/bin/touch /tmp/test-$when.txt
	
		[aliases]
			# Defining aliases here keeps [directives] cleaner, so you can
			# see what you're doing a little more easily.
			#
			$4kstream = netburn -u http://127.0.0.1/1M.bin -r 25 -o /tmp/$when.csv
	
	net-hydra requires the whenits command to be in the standard path on each client
	machine, for use precisely scheduling the directives to execute simultaneously.
~~~~

# whenits
`whenits` is a scheduler with millisecond-or-better precision.  Feed it a desired execution time in Unix epoch seconds, it will do a low-CPU-power sleep until 200ms before execution time, then a high-CPU-power loop to execute as close to the precise epoch time specified as possible.  

~~~~
Usage:

    whenits [-d] {time} {/path/to/command {arg1 arg2 arg3 ...} }

    -d            ... optional: daemonize (fork and run in background).
                      You'll need this if you're scheduling commands
                      that need to run after the current shell closes.

    time          ... Required. Execution time may be specified in absolute
                      epoch seconds, or relative to current time -
                      eg now+5s, now+10m, now+2h.

    command       ... the command to be scheduled at {time}, along with
                      any arguments to be passed to it.
~~~~
