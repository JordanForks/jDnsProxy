jDnsProxy
---------
Simple fast and lightweight DNS proxy and cache that listens on TCP or UDP ports and relays the request
to various upstream DNS-over-TCP, DNS-over-TLS, or DNS-over-HTTPS servers, optionally over http or socks 
proxies (like tor), and optionally pinning public keys for complete TLS security.  Implements a simple 
response cache respecting TTLs but also implementing proper Serve-Stale functionality.

This should support any current and future DNS record generically, as well as providing full DNSSEC support if upstream
resolvers do.

Sample/default configuration is in jdnsproxy.properties and should be documented clearly there.

Build/run like so:
```
mvn clean package
java -jar target/jDnsProxy.jar ./jdnsproxy.properties
```

Implemented specs:

  * [RFC-1035: DOMAIN NAMES - IMPLEMENTATION AND SPECIFICATION](https://tools.ietf.org/html/rfc1035)
  * [Draft: Serving Stale Data to Improve DNS Resiliency](https://tools.ietf.org/html/draft-ietf-dnsop-serve-stale)
  * [Draft: DNS Queries over HTTPS](https://tools.ietf.org/html/draft-hoffman-dns-over-https)
  * [RFC-6891: Extension Mechanisms for DNS (EDNS(0))](https://tools.ietf.org/html/rfc6891)
  * [DNS EDNS0 Option Codes (OPT)](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml#dns-parameters-11)
  * [RFC-3225: Indicating Resolver Support of DNSSEC](https://tools.ietf.org/html/rfc3225)

Use these for quick testing:
```
dig -p5353 @127.0.0.1 debian.org +tries=1 +retry=0 +tcp
dig -p5353 @127.0.0.1 debian.org +tries=1 +retry=0 +tcp +dnssec

dig -p5353 @127.0.0.1 debian.org +tries=1 +retry=0
dig -p5353 @127.0.0.1 debian.org +tries=1 +retry=0 +dnssec
```

And use this to extract TLS public keys in pinning format:
```
openssl s_client -connect 'dns.google.com:443' 2>&1 < /dev/null | sed -n '/-----BEGIN/,/-----END/p' | openssl x509 -noout -pubkey | openssl asn1parse -noout -inform pem -out /dev/stdout | openssl dgst -sha256 -binary | openssl base64
```

License
-------
GNU/GPLv3 for now, ping me if you think it should be something else, I don't care much.