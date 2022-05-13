# jarm-cpp

A C++ implementation of [JARM](https://engineering.salesforce.com/easily-identify-malicious-servers-on-the-internet-with-jarm-e095edac525a). JARM is an active TLS server fingerprinting algorithm.

# Motivation

There is a [Python](https://github.com/salesforce/jarm) and [Go](https://github.com/RumbleDiscovery/jarm-go) implementations, but I needed a C++ implementation, and a better understanding of the algorithm, so I ended up implementing it in C++.

# How it Works

The tool supports:
- querying a server for jarm hashing
- querying a list of servers and saving results into a csv file.
- printing the raw per-request fingerprint
- jarm hashing of the raw hash

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
