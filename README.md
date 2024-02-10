# 1. Webscout in a Nutshell

Webscout is a digital web investigation platform used by cyber threat intelligence analysts, OSINT specialists, journalists, law enforcement agencies, cybersecurity hobbyists, phishing victims, and many others who benefit from making sense of and findings patterns in complex internet data. 

Webscout is still in the very early phases of development. This page outlines all the features that are presently in production and accessible through our [login page](https://webscout.io/login).

# 2. Documentation

## 2.1 Ontology 

There are three distinct yet interconnected concepts that are important to understand when using Webscout: Selectors, Artefacts, and Fingerprints. If you have prior experience with (cyber) intelligence or OSINT analysis, these are most likely familiar to you. **Selectors** refer to the inputs that our search engine can process, which currently include URLs, domain names, and IP addresses. **Artefacts** represent the technical information our search engine identifies about the searched selector, and **Fingerprints** are artefacts processed in a way that makes it possible to identify, classify, and pivot between related systems on the internet.    

### 2.1.1 Selectors

Webscout adopts the term "Selector" from traditional intelligence analysis. Here, "Selector" refers to a distinct and _searchable_ attribute (currently URLs, domains, and IP addresses). [Gayde et al. (2020)](https://www.nisos.com/blog/using-selectors-open-source-intelligence/) provide the following explanation of the role of selectors in digital investigations:

> *Selectors are entities such as MAC addresses, email addresses, phone numbers, and IP addresses. Each one of these entities allows us to analyze it to determine how unique it is, and then potentially correlate it with other selectors and the entity where we found it.*
> 
> *We can then cross reference that selector in other data for greater refinement and enrichment.*

Selectors serve as the primary means to query and access relevant information from our search engine. Each selector has its own unique search engine View. This documentation covers each distinct Selector View. 

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

Webscout uses the concept of Fingerprints to refer to transformations of certain web artifacts, for example through different types of hashing algorithms, to make them pseudo-unique identifiers. These identifiers are crucial in recognizing, differentiating, and clustering various systems on the internet. Examples of such fingerprints include HTTP response header fingerprints, favicon hashes, JARM or JA3s hashes, among other distinctive markers.

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

A traditional method would involves a tedious multi-step process, such as the following:

1. Identify all nginx web servers on the internet.
2. Filter these servers to find the specific version (`nginx/1.3.37`).
3. Make requests to each server
4. Analyze the response headers for all matching characteristics (`Content-Type`, `X-Generator`, etc.)
5. ... and so on, until you are left with the subset of webservers that you  actually set out to identify. 

Ain't nobody got time for this type of grunt work. A more efficient approach involves condensing all the headers into a single string value, creating a **Fingerprint** of the response. This fingerprint is a compact representation of the response's characteristics.

For example, a simple fingerprint of the above HTTP response might be its `SHA-256 hash` value: `fc825d9b83f54e05c2dbb5e0275e576866938cdc3f1ea357529f56659867bda1`. A more complex fingerprint could involve using its `simhash`. Webscout computes both simple cryptographic hashes and more esoteric types of HTTP responses and similar types of artefacts.

Fingerprints are instrumental in the identification, categorization, and tracking of web entities. They are a cornerstone of threat detection and analysis, uncovering both subtle and overt anomalies and patterns in internet-connected devices. The ability to pivot seamlessly between different types of artifacts are the bread and butter of cyber threat intelligence, and we hope you will have fun deploying these techniques to identify and hunt evil with Webscout.

## 2.2 Views

Views are the user interfaces that organize and display the *Artefacts* our search engine identified on the selector you searched on. Each searchable selector (currently URLs, Domain Names, and IP-addresses) has its own dedicated **View**. 

![Screenshot 2024-01-31 164504](https://github.com/webscout-io/site/assets/47919173/d43583a7-23a1-4a3f-98a0-daef17352892)
_URL View_

![Screenshot 2024-01-31 164920](https://github.com/webscout-io/site/assets/47919173/23524999-471e-4308-ab0f-9406d2867b6f)
_Domain View_

![Screenshot 2024-01-31 165812](https://github.com/webscout-io/site/assets/47919173/aa1d89cd-ed50-48fe-8737-41cdc32a9c9a)
_IP address View_

Views are designed to streamline the process of shifting between the different Artefacts of relevance to your investigation. They seek to make it easy and intuitive to pivot and connect otherwise seemingly unrelated systems on the internet. 

The remainder of article documents all the features of each unique view. First the URL View, then the Domain View, and, finally the IP-address view. 


---
### URL View

This section documents each Artefact in Webscout's URL View. 

#### Security Vendor Flags
This number indicates how many third party web security solutions, such as anti-spam and anti-virus companies, that have flagged the searched URL as malicious. The purpose is to give different expert opinions on the same selector in a VirusTotal-like manner. Currently, the feature only supports [Google Web Risk](https://cloud.google.com/security/products/web-risk). 

#### Indicator Feeds
This number indicates how many publicly available threat/IoC feeds that have flagged the searched URL as malicious. The cybersecurity community is booming with quality threat intelligence. Unfortunately most of it is scattered across the web, and there are no good hubs that collect it. That is exactly what this feature hopes provide: a single point of entry into all of the best sources of raw cyber threat intelligence. If there are any great resources that we have missed, please make an issue on this repo. ❤️

#### Threat Intel Mentions
This number indicates how many times the searched URL has appeared in articles or blog posts by reputable sources of threat research.

It is important to note that this evaluation is limited to publicly accessible articles only. A significant portion of the high-quality threat intelligence generated by entities such as CrowdStrike, Mandiant, ESET, etc. is exclusively available through their paid services which are unfortunately a bit out of our budget.

#### Phishing Probability
This score is derived from our AI model which is based on [Gradient Boosting](https://en.wikipedia.org/wiki/Gradient_boosting) We continuously update this model with data on new phishing kits, frameworks, and phishing websites we find in the wild. The model is biased towards generating more false positives than false negatives. This means it is more likely to incorrectly label legitimate login pages as malicious rather than erroneously identifying malicious sites as safe.

The score is still highly experimental and should not be regarded as a source of truth. A low Phishing Probability score does _NOT_ definitively confirm the safety of the URL you have searched.

#### Fingerprints
This section contains the various fingerprints that Webscout dusted from the URL (internal ref xx). Webscout currently computes the following fingerprints: 

- HTTP Fingerprint
- Server Jarm 
- Payload Hash 
- Text Simhash 
- HTML Head Hash
- HTML Head Simhash
- HTML Body Hash 
- HTML Body Simhash 

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
If you have any suggestions or feedback for which headers we should include in this fingerprint, please do not hesitate to reach out or make an issue. 🤙 

##### Server JARM
This value is the JARM-fingerprint of the IP-address that the scanned URL's domain name points to (the DNS A-record associated with the domain). JARM is a web fingerprinting technique that probes a host’s TLS implementation. It is a hybrid fuzzy hash generated by examining attributes in TLS Server Hello responses resulting from 10 different TLS Client Hello packets sent from the client to the host. JARM hashes can be used to identify clusters of potentially related  servers on the internet. For example, if a certain type of malware/ C2 framework uses a particular out-of-the-box TLS configuration, JARM can help identify other servers that might be part of the same C2 network. JARM was developed by [Salesforce’s engineering team](https://engineering.salesforce.com/easily-identify-malicious-servers-on-the-internet-with-jarm-e095edac525a/) in 2020.  

##### Payload Hash
This is the SHA256 value of the searched URL's `HTTP response body`. It is the raw response to the initial `GET`-request before any JavaScript or external resources have been loaded. It is therefore **not** the value you would get from SHA256 hashing the "view source" code you can see from within your browser after the page has loaded. 

##### Text simhash
This is the simhash of the searched URL's` HTTP response body`. The raw HTTP response from the initial `GET`-request is passed through a [GO-implementation](https://github.com/mfonda/simhash) of [Charikar's simhash algorithm](https://www.cs.princeton.edu/courses/archive/spring04/cos598B/bib/CharikarEstim.pdf) to produce the value.  

##### HTML Head Hash
This is a SHA-256 hash value of the `<head>` section of the searched URL's rendered HTML . The `<head>` section typically includes metadata, links to stylesheets, scripts, and other information that is not directly displayed in the main content of the webpage.

##### HTML Head Simhash
This is the simhash value of the `<head>` section of the searched URL's rendered HTML. For a more in-depth explanation of the significance of the HTML head section, see the paragraph "HTML Head Hash" above. The hash is generated by passing the searched URL's `<head>` section through a [GO-implementation](https://github.com/mfonda/simhash) of [Charikar's simhash algorithm](https://www.cs.princeton.edu/courses/archive/spring04/cos598B/bib/CharikarEstim.pdf).

##### HTML Body Hash
This is a SHA-256 hash value of the `<body>` section of the searched URL's rendered HTML . The `<body>` section typically contains the main content of the webpage, such as text, images, and other media. Unlike the `<head>` tag, which contains metadata like title, scripts, and links to external resources (CSS files, etc.), the `<body>` focuses mainly on the content visible to users. The value is created by applying the hash function to the entire content within the rendered `<body>` tag. This means all the text, inline styles, embedded scripts, and other elements within this section are encompassed in the value.

##### HTML Body Simhash
This is the simhash value of the `<body>` section of the searched URL's rendered HTML. The hash is generated by passing the `<head>` section through a [GO-implementation](https://github.com/mfonda/simhash) of [Charikar's simhash algorithm](https://www.cs.princeton.edu/courses/archive/spring04/cos598B/bib/CharikarEstim.pdf).

##### Favicon Hash
This value is a [perceptual hash](https://www.hackerfactor.com/blog/index.php?/archives/432-Looks-Like-It.html) of the website's favicon image (if it has any that we can identify). A perceptual hash, often abbreviated as "pHash," is a type of algorithm designed to produce a hash value of media files in a way that reflects the content's perceptual features. This means that (closely) similar looking media files will produce  identical hash values, even if there are minor variations in the files due to compression, resizing, or other forms of mild distortion. Our implementation uses [this library](https://pkg.go.dev/github.com/corona10/goimagehash#section-readme) which adopts the implementation described in the article linked above.  

##### Screenshot Phash
This value is a [perceptual hash](https://www.hackerfactor.com/blog/index.php?/archives/432-Looks-Like-It.html) of the screenshot that Webscout captured of the searched URL. Webscout waits 10-seconds for the external resources and scripts to load, and we do our best to swat away cookie pop-ups and similar annoying clutter that would otherwise obscure the vision of the website. 
As stated under "Favicon Hash" above (internal ref xx), a pHash is a type of algorithm designed to produce a hash value of media files in a way that reflects the content's perceptual features. 

### Inline Resources
This section displays information about the searched URL's inline resources. 

An inline resource on a webpage refers to any content that is directly embedded or included within the HTML document, as opposed to being loaded from an external file through a link. These resources can be part of the HTML structure itself or included through specific tags that reference external sources but are treated as an integral part of the page's content.

#### Type
Webscout currently fingerprints the following Inline Resource types:

1. _Audio_: This type is identified by the `audio` tag. Audio resources are embedded directly within the webpage, allowing users to listen to audio content without needing to navigate away from the page.
    
2. _Embedded_: Identified by the `embed` and `iframe` tags, embedded resources are used to include or embed other content within a webpage, such as videos, interactive content, or external webpages.
    
3. _Image_: The `img` and `input` tags (the latter likely referring to image inputs, such as buttons) are used to include images as inline resources. These are visual elements displayed as part of the webpage.
    
4. _Script_: Identified by the `script` tag, script resources are crucial for adding interactivity, functionality, and dynamic content to webpages through JavaScript or other scripting languages.
    
5. _Stylesheet_: This type is specific to CSS stylesheets, which define the look and layout of a webpage. They are linked via the `link` tag with a `rel` attribute value of "stylesheet".
    
6. _Icon_: Also identified through the `link` tag with a `rel` attribute of "icon", these resources represent the favicon or website icon displayed in browser tabs and bookmarks.
    
7. _Author_: Links to a webpage specifying the author of the document, identified by a `rel` attribute of "author" in the `link` tag.
    
8. _Help_: This type links to a help document, indicated by a `rel` attribute of "help" in the `link` tag.
    
9. _Search_: Represents a link to a search tool or engine for the website, marked by a `rel` attribute of "search" in the `link` tag.
    
10. _Link_: A generic category for other types of links not specifically categorized. This includes any `link` element that doesn't match the predefined `rel` attribute values listed above.
    
11. _Subtitle_: Identified by the `track` tag, subtitle resources are used for providing captions or subtitles for audio or video content.
    
12. _Video_: This type is identified by the `video` tag, allowing videos to be embedded directly into webpages for in-browser viewing.

#### Sha256
This is the SHA-256 value of the resource in question.  

#### Simhash
This is the simhash of the resource in question. Again, a [GO-implementation](https://github.com/mfonda/simhash) of [Charikar's simhash algorithm](https://www.cs.princeton.edu/courses/archive/spring04/cos598B/bib/CharikarEstim.pdf) is used to generate the hash to provide a standardize fingerprint of the resource artefact. 

#### Relations
This number indicates how many Selectors (ref xx.) this particular Resource artefact has been observed on (based on its SHA256-value). Clicking the number will open a pane that displays where exactly Webscout has observed the resource. 

### Requests
This section details requests for external resources initiated by visiting the provided URL through a browser. In this context, "external" just means that the request was made to "somewhere else" on the web, in contrast to the Inline Resources described previously (internal ref xx). This includes resources from different subdomains or URLs, even if they belong to the same domain as the provided URL.

Why would one want to pay attention to the requests a URL makes upon loading it? Here is a short list of examples. 

- **Phishing and Scams:** A site might load stylesheets and images that mimic a legitimate site to deceive users into disclosing sensitive information.
- **Resource Abuse:** Some sites may load cryptomining scripts or unwanted software, using the visitor's resources without consent.
- **Third-party Vulnerabilities:** Reliance on compromised or malicious third-party resources can make otherwise benign sites vectors for attack.

It's crucial to understand that the requests listed in this section reflect those observed when the URL is accessed via a browser under standard conditions. Requests that result from more complex interactions, such as submitting forms or activating webhooks, are not included in this overview.... yet 🤫([suspenseful music](https://www.youtube.com/watch?v=j2d6T5G2rrY)). 

#### Method
The [HTTP request method](https://en.wikipedia.org/wiki/HTTP#Request_methods) observed in the connection to the external resource. The most common one (by far) is `GET`, followed by `POST`, `HEAD`, `PUT`, etc.

#### Status
The HTTP response status code observed in the request to the external resource that was requested. 

- `1XX` - I have never seen this before so it probably doesn't matter ¯\\_(ツ)_/¯
- `2XX` - The request was successful
- `3XX` - The request got redirected
- `4XX` - Client error
- `5XX` - Server error

#### Resource
The name of the resource that was requested as indicated by the final part of the full URL in the request to the external resource. From Mozilla's web docs: 

> The target of an HTTP request is called a "resource", whose nature isn't defined further; it can be a document, a photo, or anything else. Each resource is identified by a Uniform Resource Identifier ([URI](https://developer.mozilla.org/en-US/docs/Glossary/URI)) used throughout HTTP for identifying resources. ([Source](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/Identifying_resources_on_the_Web))

#### Domain
The domain hosting the external resource that was requested. 

#### Mime
The the MIME type associated with the external resource that requested. This is determined by the HTTP response; not some magic file analysis on our end.  

#### Size
This is the size (in bytes) of  the external resource that was requested. Importantly, it is not the size "on the wire", which would be slightly bigger as a result of TCP and TLS overhead. Size is derived from the `Content-Length` header in the HTTP response ([ref](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Length)).

#### IP
This is the IP-address that hosts the domain that the external resource was requested from; the domain's [DNS A record](https://www.cloudflare.com/learning/dns/dns-records/dns-a-record/). 

### Page Extracts
Potentially sensitive data extracted from the URL, such as leaked API keys, IP addresses, e-mails, etc. Like other regex-based extractors, it is prone make somewhat whack, albeit theoretically correct, identifications. For example, it might extract "`::`" as an IP version 6 address. And although `::` technically is a valid IP6 address, it is unlikely to be an IP version 6 address in the context it was extracted from. Same goes with API keys and the other types of digital "nuggets" we extract. If you want to learn more about this type of analysis, go check projects such as [trufflehog](https://github.com/trufflesecurity/trufflehog). 

#### Type
Webscout currently extracts the following potentially sensitive data from URLs: 
- File hash values
- API keys to various popular services
- Crypto wallets
- Email addresses
- IP addresses
If we are missing something important from an CTI/OSINT perspective, please make an issue and we will implement it ASAP. 🫶
#### Value
The raw value of the extracted data. 

### Screenshot
The screenshot that our bot managed to capture of the URL's rendered HTML. The bot waits 10-seconds for the external resources and scripts to load. We do our best to swat away cookie pop-ups and similar annoying clutter that would otherwise obscure your vision of the website. 

#### View source
The raw rendered HTML source code of the url. Similar to what you would see by clicking "view source" if you visited the URL from your own browser. The bot waits 10-seconds for the external resources and scripts to load. If for some reason not all resources have loaded, it simply captures the state of the source as it is at that time once the 10 seconds have passed.  

### Technology
Web technologies we were able to identify on the website. Similar to [Wappalyzer](https://github.com/tunetheweb/wappalyzer) but based on custom [nuclei](https://github.com/projectdiscovery/nuclei) templates. 

### Trackers
Search Engine Optimization (SEO) trackers we were able to identify on the website. From Wikipedia ([source](https://en.wikipedia.org/wiki/Web_tracking)): 

> _Web tracking_ is the practice by which operators of [websites](https://en.wikipedia.org/wiki/Website "Website") and third parties collect, store and share information about visitors’ activities on the [World Wide Web](https://en.wikipedia.org/wiki/World_Wide_Web "World Wide Web"). Analysis of a user's behaviour may be used to provide content that enables the operator to infer their preferences and may be of interest to various parties, such as advertisers.[[1]](https://en.wikipedia.org/wiki/Web_tracking#cite_note-1)[[2]](https://en.wikipedia.org/wiki/Web_tracking#cite_note-2) Web tracking can be part of visitor management.[[3]](https://en.wikipedia.org/wiki/Web_tracking#cite_note-3)

Webscout currently identify trackers from the following providers: 
- Google 
- Facebook
- Cloudflare 
- LinkedIn

... and more are underway! If there are any trackers you find particularly interesting from a CTI perspective, don't hesitate to make an issue and we will add it. 

### Socials
Social media links and handles carved from the rendered page source. Largely based on [this list,](https://github.com/senran101604/sagemode/blob/master/sites.py) shout-out senran101604. 

### Cookies
The [HTTP cookies](https://en.wikipedia.org/wiki/HTTP_cookie) that were set by the website. The lock icon indicates whether the cookie is secure or not. A _secure cookie_ can only be transmitted over an encrypted connection (i.e. [HTTPS](https://en.wikipedia.org/wiki/HTTP_Secure "HTTP Secure")). They cannot be transmitted over unencrypted connections (i.e. [HTTP](https://en.wikipedia.org/wiki/HTTP "HTTP")). A cookie is made secure by adding the `Secure` flag to the cookie.

### Linked Domains
Domains embedded in the URL's Inline Resources (internal ref xx). More specifically the [hyperlinks](https://en.wikipedia.org/wiki/Hyperlink) that were carved from the rendered HTML's `href` sections, forms, etc.


---
## Domain View

This section documents each Artefact in Webscout's Domain View. 

#### Security Vendor Flags
This number indicates how many third party web security solutions, such as anti-spam and anti-virus companies, that have flagged the searched domain as malicious. The purpose is to give different expert opinions on the same selector in a VirusTotal-like manner. Currently, the feature only supports [Google Web Risk](https://cloud.google.com/security/products/web-risk). 

#### Indicator Feeds
This number indicates how many publicly available threat/IoC feeds that have flagged the searched domain as malicious. The cybersecurity community is booming with quality threat intelligence. Unfortunately most of it is scattered across the web, and there are no good hubs that collect it. That is exactly what this feature hopes provide: a single point of entry into all of the best sources of raw cyber threat intelligence. If there are any great resources that we have missed, please make an issue on this repo. ❤️

#### Threat Intel Mentions
This number indicates how many times the searched domain has appeared in articles or blog posts by reputable sources of threat research.

It is important to note that this evaluation is limited to publicly accessible articles only. A significant portion of the high-quality threat intelligence generated by entities such as CrowdStrike, Mandiant, ESET, etc. is exclusively available through their paid services which are unfortunately a bit out of our budget.

#### Phishing Probability
This score is derived from our AI model which is based on [Gradient Boosting](https://en.wikipedia.org/wiki/Gradient_boosting) We continuously update this model with data on new phishing kits, frameworks, and phishing websites we find in the wild. The model is biased towards generating more false positives than false negatives. This means it is more likely to incorrectly label legitimate login pages as malicious rather than erroneously identifying malicious sites as safe.

The score is still highly experimental and should not be regarded as a source of truth. A low Phishing Probability score does _NOT_ definitively confirm the safety of the domain you have searched.

### Historic Resolves
Previously observed Type A DNS records for the searched domain. The data is based on Webscout's own active DNS data (see [active vs passive DNS](https://knowledge.whoisxmlapi.com/active-vs-passive-dns#)). 

#### IP
The historic DNS Type A record(s) ([A](https://www.cloudflare.com/en-gb/learning/dns/dns-records/dns-a-record/) or [AAAA](https://www.cloudflare.com/en-gb/learning/dns/dns-records/dns-aaaa-record/)) for the searched domain. 

#### Country 
The geographic location associated with the IP address. 

#### AS  Number 
The autonomous system number ([ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet))) associated with the IP address.

#### AS Organization
The autonomous system organization (ASO) associated with the ASN. See [What is an autonomous system](https://www.cloudflare.com/en-gb/learning/network-layer/what-is-an-autonomous-system/)?

### Subdomains
The Subdomains Webscout was able to identify on the searched domain. We gather these subdomains through various open sources, such as [Certificate Transparency Logs](https://certificate.transparency.dev/howctworks/), and proprietary data partnerships, such as with [ProjectDiscovery](https://projectdiscovery.io). The subdomains are queried passively from our database, but all manually searched domains enter a queue for our active subdomain discovery pipeline. In many cases, this pipeline will uncover many more subdomains that automatically will appear in the Domain View next time the domain is searched. Therefore, if you check back on the same search a couple of minutes later, there will likely be more subdomains for you to investigate. 

#### Domain 
The subdomain(s) we were aware of at the time that the domain was searched. 

#### IP 
The subdomain's DNS Type A record(s) ([A](https://www.cloudflare.com/en-gb/learning/dns/dns-records/dns-a-record/) or [AAAA](https://www.cloudflare.com/en-gb/learning/dns/dns-records/dns-aaaa-record/)). 

### Typesquat Domains 
Active* domains with similar looking looking names as commonly seen in phishing attacks, brand impersonation, and other types of digital fraud. For example, exampIe.com (with a capitalised 'i') is a typosquat domain of example.com (with a lowercase 'L'). The domains are identified by generating permutations of the searched domain and then actively querying them in the Domain Name System (DNS) to see if they are pointing to an IP address or not. See [dnstwist](https://github.com/elceef/dnstwist) for a very similar approach. 

\*Active in the sense that they have have a DNS record; _not_ in the sense that they are actively being hosted on a web server. This is currently not a check we perform. 

#### Domain
The similar looking domain(s). 
#### IPs
The DNS Type A record(s) ([A](https://www.cloudflare.com/en-gb/learning/dns/dns-records/dns-a-record/) or [AAAA](https://www.cloudflare.com/en-gb/learning/dns/dns-records/dns-aaaa-record/)) of the similar looking domain(s)

#### Country
The geographic location associated with the IP address (i.e. the similar looking domain's DNS A-record). 

#### AS Number
The autonomous system number ([ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet))) associated with the IP address (i.e. the similar looking domain's DNS A-record). 

#### AS Organization
The autonomous system organization (ASO) associated with the ASN. See [What is an autonomous system](https://www.cloudflare.com/en-gb/learning/network-layer/what-is-an-autonomous-system/). 

### Whois
The parsed WHOIS record of the searched domain. WHOIS  is a protocol used to fetch information about the ownership of domai, IP address, AS Number, etc. The protocol delivers the registrant's contact information, registration dates, updating dates, expiration date of the domain name, and so on. WHOIS responses are largely unstandardized and we do our best to parse them. We cannot guarantee that no information is lost in this process so if you would rather prefer to see the raw WHOIS response in the View, please write it in an issue on this repo. ✌️

### E-mails
The emails we were able to identify on the searched domain. Some of the results are from our own web crawlers, others are from proprietary data partnerships, such as [intelx.io](https://intelx.io) (go check them out, they're awesome). Importantly, we do not validate any of the e-mails. For this reason, some bogus e-mail addresses will exist. 

---
## IP-address View
This section documents each Artefact in Webscout's Domain View. 

#### Security Vendor Flags
This number indicates how many third party web security solutions, such as anti-spam and anti-virus companies, that have flagged the searched IP as malicious. The purpose is to give different expert opinions on the same selector in a VirusTotal-like manner. Currently, the feature only supports [Google Web Risk](https://cloud.google.com/security/products/web-risk). 

#### Indicator Feeds
This number indicates how many publicly available threat/IoC feeds that have flagged the searched IP as malicious. The cybersecurity community is booming with quality threat intelligence. Unfortunately most of it is scattered across the web, and there are no good hubs that collect it. That is exactly what this feature hopes provide: a single point of entry into all of the best sources of raw cyber threat intelligence. If there are any great resources that we have missed, please make an issue on this repo. ❤️

#### Threat Intel Mentions
This number indicates how many times the searched IP has appeared in articles or blog posts by reputable sources of threat research.

It is important to note that this evaluation is limited to publicly accessible articles only. A significant portion of the high-quality threat intelligence generated by entities such as CrowdStrike, Mandiant, ESET, etc. is exclusively available through their paid services which are unfortunately a bit out of our budget.

### Ports
The open ports Webscout has observed on the searched IP. Webscout currently sweeps the top 200 most common ports on the entire IP version 4 space (0.0.0.0/0) once every two weeks. 

Our scanning is based on TCP SYN-scanning (also known as [TCP stealth scanning](https://nmap.org/book/synscan.html)). In simple terms, this means that we do not probe the hosts for any other information than whether it listens on a given port, for example 22, or not. By initiating a TCP connection with a SYN packet and monitoring the response, we can determine whether a port is open (SYN-ACK) or closed (RST). This approach minimizes the interaction with the target host, reducing the stress on the remote host and also enhancing the stealthiness of the scan by not leaving any application level logs.

We hope to expand this capability to include full service discovery with CPEs on all the most commonly used ports, but this requires that we acquire our own dedicated IP version 4 address space. 

#### Port
The port we got a SYN-ACK response from ()

#### Service guess
The service/application associated with the open port. We try to guess the service based on how it is normally used across the internet and based on the host's operating system (derived  its TTL value). For example, port 22 is commonly used for SSH, but it can be used for anything else; it is up to the host's administrator. The guess is based on a simple lookup in a table like [this](https://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers) on Wikipedia. At least until we can begin to perform our own service discovery from a dedicated IP4 segment. 

#### TTL
The Time To Live ([TTL](https://datatracker.ietf.org/doc/html/rfc791)) observed in the TCP response from the remote host. 

#### CPE
[CURRENTLY NOT SUPPORTED]

### Reverse DNS 
The domains with DNS Type A-records that point to the searched IP address. From Cloudflare's wiki ([source](https://www.cloudflare.com/en-gb/learning/dns/glossary/reverse-dns/)): 
> A reverse DNS lookup takes an IP address and returns the domain name associated with that IP. A forward DNS lookup does just the opposite.
