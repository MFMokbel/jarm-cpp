# jarm-cpp

A C++ implementation of [JARM](https://engineering.salesforce.com/easily-identify-malicious-servers-on-the-internet-with-jarm-e095edac525a). JARM is an active TLS server fingerprinting algorithm.

# Motivation

There is a [Python](https://github.com/salesforce/jarm) and [Go](https://github.com/RumbleDiscovery/jarm-go) implementations, but I needed a C++ implementation, and a better understanding of the algorithm, so I ended up implementing it in C++.

# How it Works

The tool supports:
* querying a server
  - ex., jarmcpp.exe -d google.com -p 443 -v
  - if the option '-p' is not specified, 443 is assumed
  - '-v' is for printing the raw jarm fingerprint of every request/response
* querying a list of servers and saving results into a csv file.
  - ex., jarmcpp.exe -f <file_domains_list> -c
  - '-c' is for saving the file to a csv file with the name "jarmcpp_<date>_<time>_<am|pm>.csv"
* printing the raw per-request fingerprint
   - '-v' (for ex., jarmcpp.exe -d mfmokbel.com -v)
``` 
    JARM (mfmokbel.com [162.241.226.211]:443): 29d29d15d29d29d00042d42d000000cd600c085f371f8533aaf66051f8e5b1

    01: c02f|0303|h2|ff01-0000-0001-000b-0023-0010-0017
    02: c02f|0303|h2|ff01-0000-0001-000b-0023-0010-0017
    03: 009e|0303|h2|ff01-0000-0001-0023-0010-0017
    04: c02f|0303|http/1.1|ff01-0000-0001-000b-0023-0010-0017
    05: c02f|0303|http/1.1|ff01-0000-0001-000b-0023-0010-0017
    06: |||
    07: 1302|0303||002b-0033
    08: 1302|0303||002b-0033
    09: |||
    10: |||
```
* jarm hashing of the full raw fingerprint
  - ex., jarmcppx.exe -r "<full_raw_fingerprint>"
``` 
    jarmcppx.exe -r "c02f|0303|h2|ff01-0000-0001-000b-0023-0010-0017,c02f|0303|h2|ff01-0000-0001-000b-0023-0010-0017,009e|0303|h2|ff01-0000-0001-0023-0010-0017,c02f|0303|http/1.1|ff01-0000-0001-000b-0023-0010-0017,c02f|0303|http/1.1|ff01-0000-0001-000b-0023-0010-0017,|||,1302|0303||002b-0033,1302|0303||002b-0033,|||,|||" 
```
 Output:
 ```
    JARM ( []:443): 29d29d15d29d29d00042d42d000000cd600c085f371f8533aaf66051f8e5b1
 ```
# Third-party libraries used

- [Hash-Library: for SHA-256](https://github.com/stbrumme/hash-library)
- [SFML: for socket communications](https://www.sfml-dev.org/index.php)
- [Color Console: for console coloring](https://github.com/imfl/color-console)
 
# History
 
 - First release on May 13, 2022.
 
# Contributing

Open for pull requests and issues. Comments and suggestions are greatly appreciated.

# Author

Mohamad Mokbel ([@MFMokbel](https://twitter.com/MFMokbel))
