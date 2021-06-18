# What it is

<div style="text-align: justify">
Uanataca qualified timestamp service is the only one that has full legal value, with reversal of the burden of proof, as required by the European Regulation 910/2014 and in many other international regulations.
<br></br>
The service provides an easy and low-cost method to apply reliable date and time and integrity on documents or transactions.
<br></br>
It is an online service that provides irrefutable proof regarding to the existence of certain electronic data before a particular time.

</div>

# How it works

<div style="text-align: justify">
Unlike other Uanataca services, timestamp service does not make use of a RESTful API in its process. It simply performs request and response messages over TCP (Transmission Control Protocol) in compliance to the Internet standard IETF-PKIX RFC-3161 “Internet X.509 Public Key Infrastructure Time-Stamp Protocol (TSP)”.
<br></br>
The process starts when a client generates a hash value representing the data that is required to stamp. Then, the client sends a timestamp request to the Time Stamp Authority (TSA) server including the hash value and the correct Billing service credentials.
<br></br>
The TSA responds generating a signed timestamp token including the initial hash with an attachment of the current date and time.
</div>
<br></br>

<img style="width: 80%; display: block; margin-left: auto; margin-right: auto" src="https://github.com/UANATACA/TSA-REPO/raw/main/img/tsa_hiw.png"/> 

# Requirements

Uanataca time-stamping is an online service that requires an endpoint url with authentication.

## Endpoint URL

Uanataca qualified time-stamping service is exposed in:

**https://tsa.uanataca.com/tsa/tss03**


## Authentication

The service requires a Uanataca **Billing account**. Billing credentials are composed by username and password.


# Workflow

Timestamp service process involves the following 3 steps:
<br></br>


> STEP 1: Hash generation

The original document is not sent to the TSA service. Instead is sent a hash of the document to the service. The hash doesn't contain document information. The timestamp request .tsq digest output value is created using a SHA256 hash algorithm.

**Example**

Hash generation using openssl and a SHA256 hash algorithm:

	openssl ts -query -data testfile.pdf -no_nonce -sha256 -out request.tsq

**Response**

The .tsq file ready to be included in the request call to the TSA server.
<br></br>


> STEP 2: Timestamp request

An HTTP request to the TSA server sending the calculated hash value .tsq. At this point is required the hash file, the server URL and the client billing credentials to be included in the request. 

**Example**

	// Linux
	curl -H 'Content-Type:application/timestamp-query' --data-binary @request.tsq https://tsa.uanataca.com/tsa/tss03 -o response.tsr -u billing_username:billing_password

	// Windows
	curl -H "Content-Type:application/timestamp-query" --data-binary @request.tsq https://tsa.uanataca.com/tsa/tss03 -o response.tsr -u billing_username:billing_password


**Response**

The .tsr file that contains the hash file with the timestamp ready for verification.
<br></br>


> STEP 3: Verification

It is recommended to verify the timestamp response to complete the process.

**Example**

	openssl ts -reply -in response.tsr -text

**Response**

	1 | Status info:
	2 | Status: Granted.
	3 | Status description: unspecified
	4 | Failure info: unspecified
	5 | 
	6 | TST info:
	7 | Version: 1
	8 | Policy OID: 0.4.0.2023.1.1
	9 | Hash Algorithm: sha256
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

