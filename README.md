# **Webscout in a Nutshell**

Webscout is a

# Views


Views are essentially dashboards that present relevant information about supported web selectors. We currently support three web selector types: URLs, Domains, and IP-addresses. Views make it easy to pivot between different types of selectors and fingerprints that might be relevant to your investigation.

## URLs

If you search on an IP-address, you will be

### Security Vendor Flags
This indicates if the searched URL has been flagged as malicious by any third-party entities, such as search engines and cyber threat intelligence vendors. The purpose is to give different expert opinions in a VirusTotal-like manner. Currently, this feature only supports Google Web Risk (https://cloud.google.com/security/products/web-risk). 

### Indicator Feeds
This indicates if the searched URL has appeared in any popular threat feeds. The cybersecurity community is booming with quality threat intelligence research offered completely free of charge for anyone to use. The problem is that these feeds are scattered across the web, making it very difficult for ordinary users to find them. This feature provides a single point of entry into more than 100 quality sources of cyber threat intelligence. If you are aware of any other great resources that we have missed, please make an issue. ❤️

### Threat Intel Mentions
This indicates if the searched URL has appeared in articles or blogposts published by trusted sources of cyber threat intelligence. Please be aware that this check is only performed on publicly available articles. Much of the high quality threat intelligence produced by these sources is only offered through commercial solutions. 

### Phishing Probability
This score is generated by our AI model. It is based on extreme gradient boost classification, and we train it on emerging phishing kits, frameworks, and phishing sites in the wild. Consider it only slightly more accurate than a coin toss. It is NOT a source of truth. The model is  biased towards producing more false positives than false negatives. In other words, it tends to classify more legitimate login sites as malicious than malicious sites as benign. The model is still in an experimental phase. 

### Fingerprints
In the realm of OSINT and cyber threat intelligence, web fingerprints play an important role in identifying, tracking, and understanding digital threats. These fingerprints are unique identifiers - typically hashes of specific web artefacts such as HTTP request headers or TLS configuration - that can be used to detect patterns and cluster groups of potentially related infrastructure. It is our goal to make these fingerprints pivotable to make it easy and intuitive to hunt threats in cyberspace. 

#### HTTP Fingerprint
This value is a SHA256 hash of the HTTP response headers from the web server that hosts the URL. To keep the fingerprint as robust as possible, we omit headers with highly variable value fields, such as timestamps, since inclusion of these would make it impossible to group otherwise related infrastructure. We currently omit the following headers: 
```
Accept-Language
Date
Expires
Content-Length
If-Modified-Since
Last-Modified
Set-Cookie
```
If you have any suggestions or feedback for which headers we should include in this fingerprint, please do not hesitate to reach out or make an issue.

#### Server JARM
This value is the JARM-fingerprint of the IP-address that the scanned URL's domain name points to (the DNS A-record associated with the domain). JARM is a web fingerprinting technique that probes a host’s TLS implementation. It is a hybrid fuzzy hash generated by examining attributes in TLS Server Hello responses resulting from 10 different TLS Client Hello packets sent from the client to the host. JARM hashes can be used to identify clusters of potentially related  servers on the internet. For example, if a certain type of malware/ C2 framework uses a particular out-of-the-box TLS configuration, JARM can help identify other servers that might be part of the same C2 network. JARM was developed by Salesforce’s engineering team in 2020 ([https://engineering.salesforce.com/easily-identify-malicious-servers-on-the-internet-with-jarm-e095edac525a/](https://engineering.salesforce.com/easily-identify-malicious-servers-on-the-internet-with-jarm-e095edac525a/)).  

#### Payload Hash
This is the SHA256 value of the searched URL's HTTP response body. It is the raw response to the initial GET-request before any JavaScript or external resources have been loaded. It is therefore **not** the value you would get from SHA256 hashing the "view source" code you can see from within your browser after the page has loaded. 

#### Text simhash
This is the simhash of the searched URL's HTTP response body. The raw response from the initial GET-request is passed through a [GO-implementation](https://github.com/mfonda/simhash) of [Charikar's simhash algorithm](https://www.cs.princeton.edu/courses/archive/spring04/cos598B/bib/CharikarEstim.pdf) to produce the value you see in the UI.  

#### HTML Head Hash
This is a SHA-256 hash value of the `<head>` section of the searched URL's rendered HTML . The `<head>` section typically includes metadata, links to stylesheets, scripts, and other information that is not directly displayed in the main content of the webpage. The hash of the head section can be used to quickly determine if any changes have been made to the metadata, linked resources, or scripts in a webpage without needing to examine the entire content of the head section.

#### HTML Head Simhash
This is the simhash value of the `<head>` section of the searched URL's rendered HTML. For a more in-depth explanation of the significance of the HTML Head section, see the paragraph "HTML Head Hash" above. The hash is generated by passing the searched URL's `<head>` section through a [GO-implementation](https://github.com/mfonda/simhash) of [Charikar's simhash algorithm](https://www.cs.princeton.edu/courses/archive/spring04/cos598B/bib/CharikarEstim.pdf).

#### HTML Body Hash


#### HTML Body Simhash


#### Favicon Hash
This value is a [perceptual hash](https://www.hackerfactor.com/blog/index.php?/archives/432-Looks-Like-It.html) of the website's favicon image (if it has any that we can identify). A perceptual hash, often abbreviated as "pHash," is a type of algorithm designed to produce a hash value of digital media files (most commonly images and videos) in a way that reflects the content's perceptual features. This means that (closely) similar looking media files will produce  identical hash values, even if there are minor variations in the files due to compression, resizing, or other forms of mild distortion. Our implementation uses [this library](https://pkg.go.dev/github.com/corona10/goimagehash#section-readme) which adopts the implementation described in the article linked above.  

#### Screenshot Phash
This value is a [perceptual hash](https://www.hackerfactor.com/blog/index.php?/archives/432-Looks-Like-It.html) of the screenshot that our bot captured of the searched URL. A perceptual hash, often abbreviated as "pHash," is a type of algorithm designed to produce a hash value of digital media files (most commonly images and videos) in a way that reflects the content's perceptual features. This means that (closely) similar looking media files will produce  identical hash values, even if there are minor variations in the files due to compression, resizing, or other forms of mild distortion. Our implementation uses [this library](https://pkg.go.dev/github.com/corona10/goimagehash#section-readme) which adopts the implementation described in the article linked above. 

### Inline Resources

_Type_

_Sha256_

_Simhash_

_Relations_

### Requests

_Method_

_Status_

_Resource_

### Page Extracts

_Type_

_Value_

_Source_

### Screenshot

_View source_

_Fullscreen_

**Technology**

**Trackers**

Socials

Cookies

**Linked Domains**

## Domains

R