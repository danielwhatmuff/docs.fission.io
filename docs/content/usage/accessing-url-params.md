+++
title = "Accessing URL parameters"
date =  2018-10-25T17:39:41+08:00
weight = 5
+++

To develop an application consists with REST APIs, we may want to access URL parameters in functions.

For example, a REST API with URL parameters like following  

```bash
http://192.168.0.1/guestbook/messages/{id}
```

You can put parameter placeholders in value of `--url` flag. 
Since fission uses gorilla/mux as underlying URL router, you can also write regular expression to filter out illegal API requests.

```bash
$ fission httptrigger create --method GET \
    --url "/guestbook/messages/{id}" --function restapi-get

$ fission httptrigger create --method GET \
    --url "/guestbook/messages/{id:[0-9]+}" --function restapi-get
```

Next step is to access the value of URL parameters. 

Due to some internal mechanism, the value of URL parameters will be attached to the HTTP request header like following.

```plaintext
Accept-Encoding: gzip
Host: 172.17.0.25:8888
Connection: close
Accept: */*
User-Agent: curl/7.54.0
Content-Length: 15
Content-Type: application/json
X-Forwarded-For: 172.17.0.1

X-Fission-Function-Uid: 82c95606-9afa-11e8-bbd1-08002720b796
X-Fission-Function-Resourceversion: 480652
X-Fission-Function-Name: reqpayload
X-Fission-Function-Namespace: default

X-Fission-Params-Id: 12345
```

The header with key prefix `X-Fission-Params-` are the actual fields contain value of URL parameters we want to access to.

One thing worth to notice is in some language like Go the header key will be displayed as `MIME canonical format`. For example,

```bash
url: /guestbook/messages/{id}
header key: X-Fission-Params-Id

url: /guestbook/messages/{FooBar}
header key: X-Fission-Params-Foobar
```

You have to check the letter case of header key and do conversion if necessary in order to get the right parameter value. 

(In Go, you can call `request.Header.Get()` to get header value without worrying key cases.) 

