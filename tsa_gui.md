# Introduction

## What it is

SmartTSA is an online service that provides irrefutable proof regarding to the existence of certain electronic data before a particular time, the time at which the timestamp was issued, and certifies such data has not been altered ever since.

## How it works

Unlike other Uanataca services, SmartTSA does not make use of RESTful API’s in its process. It simply performs request and response messages over TCP (Transmission Control Protocol) in compliance to the Internet standard IETF-PKIX RFC-3161 “Internet X.509 Public Key Infrastructure Time-Stamp Protocol (TSP)”.

The process starts when a client generates a hash value representing the data that is required to stamp. 

Secondly, the client sends a timestamp request to the Time Stamp Authority (TSA) server including the hash value and the correct service credentials.

The TSA responds generating a signed timestamp token including the initial hash with an attachment of the current date and time, the source of which is reliable and synchronized to UTC (Universal Time Coordinated).

# Workflow

## Hash Generation

Creation of a .tsq digest output value using a hash algorithm for timestamping. The original document is not sent to the server. 

**Example**

Hash generation using openssl and a SHA1 hash algorithm:

	1 | openssl ts -query -data testfile.pdf -no_nonce -sha1 -out request.tsq

**Response**

The .tsq file ready to be included in the request call to the TSA server.

## Requests

An HTTP request to the TSA server using the calculated hash value is sent. At this point the hash file, the server URL and the client billing credentials must be included. 

**Example**

	1 | curl -H “Content-Type:application/timestamp-query” --data-binary @request.tsq https://tsa.uanataca.com/tsa/tss03 -o response.tsr -u user:password

**Response**

The .tsr file (the hash file with the timestamp) ready for verification.

## Verification

The process is completed when the requested timestamp is verified on the hash file.

	1 | openssl ts -reply -in response.tsr -text

**Output Example**

	1 | Status info:
	2 | Status: Granted.
	3 | Status description: unspecified
	4 | Failure info: unspecified
	5 | 
	6 | TST info:
	7 | Version: 1
	8 | Policy OID: 0.4.0.2023.1.1
	9 | Hash Algorithm: sha512
	10| Message data:
	11|     0000 - 22 e7 e9 67 5c 6b d7 ea-b6 7f 35 f0 e4 32 c6 9c   "..g\k....5..2..
	12|     0010 - 98 e0 b0 f3 9c d7 ae dc-a8 6e 23 78 9f 75 28 6d   .........n#x.u(m
	13|     0020 - e3 77 97 5e 43 12 0d 63-a7 fb 38 6b 12 af 1d 33   .w.^C..c..8k...3
	14|     0030 - 25 b8 f8 e7 ec e3 8d 74-b8 fe a9 be a9 6f 84 54   %......t.....o.T
	15| Serial number: 0x69C8C103117F52
	16| Time stamp: Nov 11 17:11:42 2020 GMT
	17| Accuracy: 0x01 seconds, unspecified millis, unspecified micros
	18| Ordering: no
	19| Nonce: unspecified
	20| TSA: DirName:/C=ES/L=Barcelona/O=UANATACA S.A./OU=TSP-UANATACA/CN=Sello cualificado de tiempo electr\xC31 | 21| \xB3nico de UANATACA \xE2\x80\x93 TSU01/2.5.4.97=VATES-A66721499
	22| Extensions:
	23| qcStatements:
	24|     0.0.......^..

# Watch on Video

<iframe width="560" height="315" src="https://www.youtube.com/embed/tUGLf5y1dEA" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>