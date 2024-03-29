# jarm-cpp

A C++ implementation of [JARM](https://engineering.salesforce.com/easily-identify-malicious-servers-on-the-internet-with-jarm-e095edac525a). JARM is an active TLS server fingerprinting algorithm.

**Note-1**: This was first presented at the [HITBSecConf2023](https://conference.hitb.org/hitbsecconf2023ams/session/commsec-exploring-jarm-an-active-tls-fingerprinting-algorithm/) conference in Amsterdam, Netherlands on April 20, 2023 [Slides](https://www.mfmokbel.com/wp-content/uploads/2024/01/exploring_jarm_mfmokbel.pdf), [YouTube Recording](https://www.youtube.com/watch?v=MbgM4G1Ixv0).

# Motivation

There is a [Python](https://github.com/salesforce/jarm) and [Go](https://github.com/RumbleDiscovery/jarm-go) implementations, but I needed a C++ implementation, and a better understanding of the algorithm, so I ended up implementing it in C++.

# How it Works

The tool supports:
* querying a server
  - ex., jarmcpp.exe -d google.com -p 443 -v
  - if the option `-p` is not specified, 443 is assumed
    - option `-p` takes a list of ports comma-separated, if necessary. The first port it connects to successfully is the one taken irrespective of whether the server accepts TLS/SSL connection over this port or not
  - `-v` is for printing the raw jarm fingerprint of every request/response
* querying a list of servers and saving results into a CSV file
  - ex., jarmcpp.exe -f <file_domains_list> -c
  - `-f` is for loading a file with the list of servers (one server per line; lines that start with ';' or '//' are ignored)
  - `-c` is for saving the results to a CSV file with the name "jarmcpp\_\<date\>\_\<time\>\_<am|pm>.csv"
* printing the raw per-request fingerprint
   - `-v` (for ex., jarmcpp.exe -d mfmokbel.com -v)
``` 
    JARM (mfmokbel.com [162.241.226.211]:443): 29d29d15d29d29d00042d42d000000cd600c085f371f8533aaf66051f8e5b1

    1 tls_1_2_forward     -> c02f|0303|h2|ff01-0000-0001-000b-0023-0010-0017
    2 tls_1_2_reverse     -> c02f|0303|h2|ff01-0000-0001-000b-0023-0010-0017
    3 tls_1_2_top_half    -> 009e|0303|h2|ff01-0000-0001-0023-0010-0017
    4 tls_1_2_bottom_half -> c02f|0303|http/1.1|ff01-0000-0001-000b-0023-0010-0017
    5 tls_1_2_middle_out  -> c02f|0303|http/1.1|ff01-0000-0001-000b-0023-0010-0017
    6 tls_1_1_middle_out  -> |||
    7 tls_1_3_forward     -> 1302|0303||002b-0033
    8 tls_1_3_reverse     -> 1302|0303||002b-0033
    9 tls_1_3_invalid     -> |||
   10 tls_1_3_middle_out  -> |||
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
 * demangling a jarm hash
   - ex., jarmcpp.exe -i 29d29d15d29d29d00042d42d000000cd600c085f371f8533aaf66051f8e5b1
 
 Output (C for Cipher, V for TLS minor version):
 ```
 1 tls_1_2_forward      -> C: 0xc02f (TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256-Secure)
                           V: 3 (TLSv1.2)
 2 tls_1_2_reverse      -> C: 0xc02f (TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256-Secure)
                           V: 3 (TLSv1.2)
 3 tls_1_2_top_half     -> C: 0x009e (TLS_DHE_RSA_WITH_AES_128_GCM_SHA256-Secure)
                           V: 3 (TLSv1.2)
 4 tls_1_2_bottom_half  -> C: 0xc02f (TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256-Secure)
                           V: 3 (TLSv1.2)
 5 tls_1_2_middle_out   -> C: 0xc02f (TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256-Secure)
                           V: 3 (TLSv1.2)
 6 tls_1_1_middle_out   -> C: no value
                           V: no value
 7 tls_1_3_forward      -> C: 0x1302 (TLS_AES_256_GCM_SHA384-Recommended)
                           V: 3 (TLSv1.2)
 8 tls_1_3_reverse      -> C: 0x1302 (TLS_AES_256_GCM_SHA384-Recommended)
                           V: 3 (TLSv1.2)
 9 tls_1_3_invalid      -> C: no value
                           V: no value
10 tls_1_3_middle_out   -> C: no value
                           V: no value

Extensions sha-256 hash: cd600c085f371f8533aaf66051f8e5b1
 ```
 The security identifier suffix that comes after the '-' indicates the security status of a given cipher. The list includes, Recommended, Secure, Weak, and Insecure.
 
  * printing jarm hash to the console in JSON format using the option `-o j`
    - ex., jarmcpp.exe -d mfmokbel.com -o j

Output:
 ```
    {"server":"mfmokbel.com","ip":"162.241.226.211","hash":"29d29d15d29d29d00042d42d000000cd600c085f371f8533aaf66051f8e5b1"}
 ```

# Third-party libraries used

- [Hash-Library: for SHA-256](https://github.com/stbrumme/hash-library)
- [SFML: for socket communications](https://www.sfml-dev.org/index.php)
- [Color Console: for console coloring](https://github.com/imfl/color-console)
- [cxxopts: for parsing command-line arguments](https://github.com/jarro2783/cxxopts)
 
# History
 
 - First release on January 24, 2024.
 
# Contributing

Open for pull requests and issues. Comments and suggestions are greatly appreciated.

# Author

Mohamad Mokbel ([@MFMokbel](https://twitter.com/MFMokbel))
