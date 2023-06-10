# Ignition
> 4lch3my | June 10, 2023
-------------------
#### Starting-Point: Learn the basics of Penetration Testing
#### [Room Link](https://app.hackthebox.com/starting-point)

## Tasks
##### 1. Which service version is found to be running on port 80?

> Deploy the machine with the Spawn Machine button, also either deploy your [PWNBox](https://help.hackthebox.com/en/articles/5185608-introduction-to-pwnbox) OR Connect to [OpenVPN](https://help.hackthebox.com/en/articles/5185687-introduction-to-lab-access) and deploy your personal KALI machine.

To start, I will use [nmap] which is default option on the personal Kali install I have. We start with a basic command
  `nmap -sC -sV -A MACHINE_IP`
The -sC flag runs scripts against open ports as well to determine if there are external/common vulnerabilities that we can use outright. The -sV probes all open ports it finds to determine if we can get the service/version information. -A uses agressive mode, so we can follow along with the commands and enumeration.
  In our NMAP results we get:

```
Starting Nmap 7.93 ( https://nmap.org ) at 2023-06-10 19:48 CEST
Nmap scan report for MACHINE_IP
Host is up (0.040s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
80/tcp open  http    nginx 1.14.2
|_http-server-header: nginx/1.14.2
|_http-title: Did not follow redirect to http://ignition.htb/
```

As we can see there is only one port open `80 - HTTP` running [nginx](https://www.nginx.com/) a popular website manager and load balancer.



What is the 3-digit HTTP status code returned when you visit http://{machine IP}/?

curl -v http://10.129.177.50

```
*   Trying 10.129.177.50:80...
* Connected to 10.129.177.50 (10.129.177.50) port 80 (#0)
> GET / HTTP/1.1
> Host: 10.129.177.50
> User-Agent: curl/7.88.1
> Accept: */*
>
< HTTP/1.1 302 Found
< Server: nginx/1.14.2
< Date: Sat, 10 Jun 2023 17:50:23 GMT
< Content-Type: text/html; charset=UTF-8
< Transfer-Encoding: chunked
< Connection: keep-alive
< Set-Cookie: PHPSESSID=u9bglb2fp6gjpmhib9h1c09tv6; expires=Sat, 10-Jun-2023 18:50:23 GMT; Max-Age=3600; path=/; domain=10.129.177.50; HttpOnly; SameSite=Lax
< Location: http://ignition.htb/
< Pragma: no-cache
< Cache-Control: max-age=0, must-revalidate, no-cache, no-store
< Expires: Fri, 10 Jun 2022 17:50:23 GMT
< Content-Security-Policy-Report-Only: font-src data: 'self' 'unsafe-inline'; form-action secure.authorize.net test.authorize.net geostag.cardinalcommerce.com geo.cardinalcommerce.com 1eafstag.cardinalcommerce.com 1eaf.cardinalcommerce.com centinelapistag.cardinalcommerce.com centinelapi.cardinalcommerce.com 'self' 'unsafe-inline'; frame-ancestors 'self' 'unsafe-inline'; frame-src fast.amc.demdex.net secure.authorize.net test.authorize.net geostag.cardinalcommerce.com geo.cardinalcommerce.com 1eafstag.cardinalcommerce.com 1eaf.cardinalcommerce.com centinelapistag.cardinalcommerce.com centinelapi.cardinalcommerce.com www.paypal.com www.sandbox.paypal.com player.vimeo.com *.youtube.com 'self' 'unsafe-inline'; img-src assets.adobedtm.com amcglobal.sc.omtrdc.net dpm.demdex.net cm.everesttech.net widgets.magentocommerce.com data: www.googleadservices.com www.google-analytics.com www.paypalobjects.com t.paypal.com www.paypal.com fpdbs.paypal.com fpdbs.sandbox.paypal.com *.vimeocdn.com i.ytimg.com s.ytimg.com data: 'self' 'unsafe-inline'; script-src assets.adobedtm.com secure.authorize.net test.authorize.net www.googleadservices.com www.google-analytics.com www.paypalobjects.com js.braintreegateway.com www.paypal.com geostag.cardinalcommerce.com 1eafstag.cardinalcommerce.com geoapi.cardinalcommerce.com 1eafapi.cardinalcommerce.com songbird.cardinalcommerce.com includestest.ccdc02.com www.sandbox.paypal.com t.paypal.com s.ytimg.com www.googleapis.com vimeo.com www.vimeo.com *.vimeocdn.com www.youtube.com video.google.com 'self' 'unsafe-inline' 'unsafe-eval'; style-src getfirebug.com 'self' 'unsafe-inline'; object-src 'self' 'unsafe-inline'; media-src 'self' 'unsafe-inline'; manifest-src 'self' 'unsafe-inline'; connect-src dpm.demdex.net amcglobal.sc.omtrdc.net www.google-analytics.com geostag.cardinalcommerce.com geo.cardinalcommerce.com 1eafstag.cardinalcommerce.com 1eaf.cardinalcommerce.com centinelapistag.cardinalcommerce.com centinelapi.cardinalcommerce.com 'self' 'unsafe-inline'; child-src http: https: blob: 'self' 'unsafe-inline'; default-src 'self' 'unsafe-inline' 'unsafe-eval'; base-uri 'self' 'unsafe-inline';
< X-Content-Type-Options: nosniff
< X-XSS-Protection: 1; mode=block
< X-Frame-Options: SAMEORIGIN
<
* Connection #0 to host 10.129.177.50 left intact
```

Add host to PiHole or /etc/hosts

Testing admin page: http://ignition.htb/admin

