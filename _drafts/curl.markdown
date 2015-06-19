---
title: Curl as utilitie set of tools
---


Get web page content
```
curl -O [filenameURL]
```

Some time the target site has you _banned access based on your browser's signature_ based in your `--user-agent`

```
curl -O [filenameURL]
curl --header "X-MyHeader: 1" www.bropages.org
```

### GET:

with JSON:

```
curl -i -H "Accept: application/json" -H "Content-Type: application/json" http://hostname/resource
```

#### Change User Agent with curl to Get URL Source Code as Different OS & Browser

The basic syntax is as follows:

```
curl -A "UserAgentString" http://url.com
```

One of the most common situations of different source HTML and CSS are for websites with stripped down mobile versions, you could retrieve iPhone-specific source code with:

```
curl -A "Mozilla/5.0 (iPhone; U; CPU iPhone OS 4_3_3 like Mac OS X; en-us) AppleWebKit/533.17.9 (KHTML, like Gecko) Version/5.0.2 Mobile/8J2 Safari/6533.18.5" http://www.apple.com
```

Some sites do this with other browsers too. This would be Chrome 12 in Mac OS X 10.6.8:

```
curl -A "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_6_8) AppleWebKit/534.30 (KHTML, like Gecko) Chrome/12.0.742.112 Safari/534.30" http://microsoft.com
```

Save file
```
curl -A "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_6_8) AppleWebKit/534.30 (KHTML, like Gecko) Chrome/12.0.742.112 Safari/534.30" -O https://remysharp.com/2015/06/09/devtool-tricks-for-when-i-forget
```

### POST:

For posting data:
```
curl --data "param1=value1&param2=value2" http://hostname/resource
```

For file upload:
```
curl --form "fileupload=@filename.txt" http://hostname/resource
```

RESTful HTTP Post:
```
curl -X POST -d @filename http://hostname/resource
```
