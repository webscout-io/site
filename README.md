# 1. Webscout in a Nutshell

Webscout is a digital web investigation platform used by cyber threat intelligence analysts, OSINT specialists, journalists, law enforcement agencies, cybersecurity hobbyists, phishing victims, and many others who benefits from making sense of and findings patterns in complex internet data. 

Webscout is currently in the very early stages of development. Our search engine is approaching completion, but the community-based investigation interface is still on the drawing board. Our goal is to launch a functional beta version of the community release by the second quarter of 2025. 

This page outlines all the features that are presently in production and accessible through our [login page](https://webscout.io/login).

# 2. Documentation

## 2.1 Ontology 

The Webscout world comprises three interconnected ontological components: Selectors, Artefacts, and Fingerprints. **Selectors** refer to the web elements that our search engine can process, which currently include URLs, domain names, and IP addresses. **Artefacts** represent the technical information our search engine identifies about the searched selector. Lastly, **Fingerprints** are artefacts processed in a way that makes it possible to identify, classify, and pivot between related systems on the internet.  


> [!Question]
> So if I identify an interesting fingerprint on a selector and then search on that fingerprint in the search engine, doesn't that make the fingerprint a selector? 🤔 

Well... yes. Bear with us. Hopefully it will all makes sense when you get started with Webscout. For now, the most important thing to note is that Webscout is an online tool to make sense of URLs, Domain Names, and IP-addresses. The next couple of sections will delve deeper into the ontological concepts of Selectors, Artefacts, and Fingerprints to offer a more comprehensive understanding of these components work in our system.

### 2.1.1 Selectors

Webscout adopts the term "Selector" from traditional intelligence analysis. In this field, "Selector" refers to a distinct and _searchable_ attribute that helps characterize and identify an entity. [Gayde et al. (2020)](https://www.nisos.com/blog/using-selectors-open-source-intelligence/) provide the following explanation of the role of selectors in digital investigations, offering insight into their application and significance:

> *Using selectors, we can identify specific entities that are unique in a digital realm that allow us to understand the “how,” “why,” and potentially “who” of suspicious activity. With this context, organizations can decide if they should dedicate resources chasing the threat.*
> 
> *Selectors are entities such as MAC addresses, email addresses, phone numbers, and IP addresses. Each one of these entities allows us to analyze it to determine how unique it is, and then potentially correlate it with other selectors and the entity where we found it.*
> 
> *We can then cross reference that selector in other data for greater refinement and enrichment.*

Selectors serve as the primary means to query and access relevant information from our search engine. Here, each selector has its own unique View, which will be described in greater detail later in the documentation. Our search engine currently supports three types of selectors: 

- URLs (for example `https://www.example.com/cms/shell.php`)
- Domains (for example `example.com`)
- IP-addresses (for example `93.184.216.34`)

### 2.1.2 Artefacts

Webscout uses the concept of "Artefacts" to refer to the range of technical details, digital evidence, and fingerprints gathered about a specific Selector. For example, a search on an URL yields the following URL artefacts:

- Cookies
- Inline resources
- Page source
- Server JARM
- HTTP response payload hashes
- Favicon and favicon perceptual hash
- Requests observed (redirects, external resources etc.)
- Page Extracts 
- Technologies identified on the webserver 
- ...etc. 

In [MISP terminology](https://www.circl.lu/doc/misp/GLOSSARY.html#misp-attribute), Artefacts are called Attributes. Some SIEMs and threat intelligence formats such as [STIX](https://stix2.readthedocs.io/en/latest/api/v20/stix2.v20.observables.html) use the term "Observable", which is essentially the same as an Artefact in Webscout terminology. 

Artefact are vital for gaining insight into the nature, behavior, and potential risks associated with the Selector under investigation.

### 2.1.3 Fingerprints

Webscout uses the concept of Fingerprints to refer to transformations of certain web artifacts, for example through different types of hashing algorithms, that act as pseudo-unique identifiers. These identifiers are crucial in recognizing, differentiating, and clustering various systems on the internet. Examples of such fingerprints include HTTP response header fingerprints, favicon hashes, JARM or JA3s hashes, among other distinctive markers.

Consider a scenario where you need to identify servers on the internet that provide a specific HTTP response, such as:
```
HTTP/1.1 200 OK
Server: nginx/1.3.37
Date: Sat, 20 Apr 2024 09:09 GMT
Content-Type: text/html
Connection: keep-alive
Vary: Accept-Encoding
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate, post-check=0, pre-check=0
Pragma: no-cache
X-Generator: Notaphish
```

A traditional method involves a multi-step process:

1. Identify all nginx web servers on the internet.
2. Filter these servers to find the specific version (`nginx/1.3.37`).
3. Make requests to each server
4. Analyze the response headers for all matching characteristics (`Content-Type`, `X-Generator`, etc.)
5. ... and so on, until you are left with the subset of webservers that you  actually set out to identify. 

This method, though straightforward, is extremely tedious. Ain't nobody got time for that. 

A more efficient approach involves condensing all the headers into a single string value, creating a **Fingerprint** of the response. This fingerprint is a compact representation of the response's characteristics, significantly simplifying the identification process. For example, a simple fingerprint of the above HTTP response might be its `SHA-256 hash` value: `fc825d9b83f54e05c2dbb5e0275e576866938cdc3f1ea357529f56659867bda1`. A more complex fingerprint could involve using its `simhash`. Webscout computes both simple cryptographic hashes and more esoteric types of HTTP responses and similar types of artefacts.

Fingerprints are instrumental in the identification, categorization, and tracking of web entities. They are a cornerstone of threat detection and analysis, uncovering both subtle and overt anomalies and patterns in web traffic and server responses. Effective fingerprinting and the ability to pivot seamlessly between different types of artifacts are the bread and butter of  cyber threat intelligence, and we hope you will have fun deploying these techniques to identify evil with Webscout.

## 2.2 Views

Views are the user interfaces that display the results (the *Artefacts*) from your searches. Views are designed to organize and present the most relevant information about the searched selector. As such, each searchable selector (currently URLs, Domain Names, and IP-addresses) has its own dedicated **View** which you are automatically directed to upon conducting a search.  

![[Pasted image 20240131164523.png]]
_URL View_

![[Pasted image 20240131164925.png]]
_Domain View_

![[Pasted image 20240131165819.png]]
_IP-address View_

Views are designed to streamline the process of shifting between the different Artefacts of relevance to your investigation. They seek to make it  easy and intuitive to pivot and connect otherwise seemingly unrelated systems on the internet. 

The remainder of article documents all the features of each unique view. First the URL View, then the Domain View, and, finally the IP-address view. 


---
### URL View

This section documents each Artefact in Webscout's URL View. 

#### Security Vendor Flags
This indicates if the searched URL has been flagged as malicious by any third-party entities, such as search engines and cyber threat intelligence vendors. The purpose is to give different expert opinions in a VirusTotal-like manner. Currently, this feature only supports [Google Web Risk](https://cloud.google.com/security/products/web-risk). 

#### Indicator Feeds
This indicates if the searched URL has appeared in any popular threat feeds. The cybersecurity community is booming with quality threat intelligence research offered completely free of charge for anyone to use. The problem is that these feeds are scattered across the web, making it very difficult for ordinary users to find them. This feature provides a single point of entry into more than 100 quality sources of cyber threat intelligence. If you are aware of any other great resources that we have missed, please make an issue. ❤️

#### Threat Intel Mentions
This indicates if the searched URL has appeared in articles or blogposts published by trusted sources of cyber threat intelligence. Please be aware that this check is only performed on publicly available articles. Much of the high quality threat intelligence produced by these sources is only offered through commercial solutions. 

#### Phishing Probability
This score is generated by our AI model. It is based on [Gradient boosting](https://en.wikipedia.org/wiki/Gradient_boosting), and we train it on emerging phishing kits, frameworks, and phishing sites observed in the wild. Consider it only slightly more accurate than a coin toss. It is NOT a source of truth. The model is  biased towards producing more false positives than false negatives. In other words, it tends to classify more legitimate login sites as malicious than malicious sites as benign. The model is still in an experimental phase. 

#### Fingerprints
In the realm of OSINT and cyber threat intelligence, web fingerprints play an important role in identifying, tracking, and understanding digital threats. These fingerprints are unique identifiers - typically hashes of specific web artefacts such as HTTP request headers or TLS configuration - that can be used to detect patterns and cluster groups of potentially related infrastructure. It is our goal to make these fingerprints pivotable to make it easy and intuitive to hunt threats in cyberspace. 

##### HTTP Fingerprint
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

##### Server JARM
This value is the JARM-fingerprint of the IP-address that the scanned URL's domain name points to (the DNS A-record associated with the domain). JARM is a web fingerprinting technique that probes a host’s TLS implementation. It is a hybrid fuzzy hash generated by examining attributes in TLS Server Hello responses resulting from 10 different TLS Client Hello packets sent from the client to the host. JARM hashes can be used to identify clusters of potentially related  servers on the internet. For example, if a certain type of malware/ C2 framework uses a particular out-of-the-box TLS configuration, JARM can help identify other servers that might be part of the same C2 network. JARM was developed by [Salesforce’s engineering team](https://engineering.salesforce.com/easily-identify-malicious-servers-on-the-internet-with-jarm-e095edac525a/) in 2020.  

##### Payload Hash
This is the SHA256 value of the searched URL's `HTTP response body`. It is the raw response to the initial `GET`-request before any JavaScript or external resources have been loaded. It is therefore **not** the value you would get from SHA256 hashing the "view source" code you can see from within your browser after the page has loaded. 

##### Text simhash
This is the simhash of the searched URL's` HTTP response body`. The raw HTTP response from the initial `GET`-request is passed through a [GO-implementation](https://github.com/mfonda/simhash) of [Charikar's simhash algorithm](https://www.cs.princeton.edu/courses/archive/spring04/cos598B/bib/CharikarEstim.pdf) to produce the value.  

##### HTML Head Hash
This is a SHA-256 hash value of the `<head>` section of the searched URL's rendered HTML . The `<head>` section typically includes metadata, links to stylesheets, scripts, and other information that is not directly displayed in the main content of the webpage. The hash of the head section can be used to quickly determine if any changes have been made to the metadata, linked resources, or scripts in a webpage without needing to examine the entire content of the head section.

##### HTML Head Simhash
This is the simhash value of the `<head>` section of the searched URL's rendered HTML. For a more in-depth explanation of the significance of the HTML head section, see the paragraph "HTML Head Hash" above. The hash is generated by passing the searched URL's `<head>` section through a [GO-implementation](https://github.com/mfonda/simhash) of [Charikar's simhash algorithm](https://www.cs.princeton.edu/courses/archive/spring04/cos598B/bib/CharikarEstim.pdf).

##### HTML Body Hash
This is a SHA-256 hash value of the `<body>` section of the searched URL's rendered HTML . The `<body>` section typically contains the main content of the webpage, such as text, images, and other media. Unlike the `<head>` tag, which contains metadata like title, scripts, and links to external resources (CSS files, etc.), the `<body>` focuses purely on the content visible to users. The value is created by applying the hash function to the entire content within the rendered `<body>` tag. This means all the text, inline styles, embedded scripts, and other elements within this section are encompassed in the value.

##### HTML Body Simhash
This is the simhash value of the `<body>` section of the searched URL's rendered HTML. The hash is generated by passing the `<head>` section through a [GO-implementation](https://github.com/mfonda/simhash) of [Charikar's simhash algorithm](https://www.cs.princeton.edu/courses/archive/spring04/cos598B/bib/CharikarEstim.pdf).

##### Favicon Hash
This value is a [perceptual hash](https://www.hackerfactor.com/blog/index.php?/archives/432-Looks-Like-It.html) of the website's favicon image (if it has any that we can identify). A perceptual hash, often abbreviated as "pHash," is a type of algorithm designed to produce a hash value of digital media files (most commonly images and videos) in a way that reflects the content's perceptual features. This means that (closely) similar looking media files will produce  identical hash values, even if there are minor variations in the files due to compression, resizing, or other forms of mild distortion. Our implementation uses [this library](https://pkg.go.dev/github.com/corona10/goimagehash#section-readme) which adopts the implementation described in the article linked above.  

##### Screenshot Phash
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


---
## Domain View


---
## IP-address View

R