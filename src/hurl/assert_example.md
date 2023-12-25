# 测试示例

## 响应头

```hurl
# Using implicit response asserts 
GET https://example.org/index.html

HTTP 200
Set-Cookie: theme=light
Set-Cookie: sessionToken=abc123; Expires=Wed, 09 Jun 2021 10:18:14 GMT

# Or explicit response asserts
GET https://example.org

HTTP 302
[Asserts]
header "Location" contains "www.example.net"
```

## 状态码测试

```hurl
# Testing 200 OK
GET https://example.org/order/435
HTTP 200


# Testing status code is in a 200-300 range
GET https://example.org/order/435

HTTP *
[Asserts]
status >= 200
status < 300

```

## HTTP 版本测试

```hurl
# You can explicitly test HTTP version 1.0, 1.1 or 2:
GET https://example.org/http10
HTTP/1.0 200

GET https://example.org/http11
HTTP/1.1 200

GET https://example.org/http2
HTTP/2 200

# Or simply use HTTP to not test version!
GET https://example.org/http2
HTTP 200
```

## XPath 测试

```
# XPath asserts can be used to check HTML content            
GET https://example.org

HTTP 200
Content-Type: text/html; charset=UTF-8
[Asserts]
xpath "string(/html/head/title)" contains "Example" # Check title
xpath "count(//p)" == 2                             # Check the number of p
xpath "//p" count == 2                              # Similar assert for p
xpath "boolean(count(//h2))" == false               # Check there is no h2  
xpath "//h2" not exists                             # Similar assert for h2
xpath "string(//div[1])" matches /Hello.*/
```

## JSONPath 测试

```hurl
# Testing a JSON response with JSONPath
GET https://example.org/api/tests/4567

HTTP 200
[Asserts]
jsonpath "$.status" == "RUNNING"    # Check the status code
jsonpath "$.tests" count == 25      # Check the number of items
jsonpath "$.id" matches /\d{4}/     # Check the format of the id  
```

## Set-Cookie 测试

```hurl
GET http://myserver.com/home

HTTP 200
[Asserts]
cookie "JSESSIONID" == "8400BAFE2F66443613DC38AE3D9D6239"
cookie "JSESSIONID[Value]" == "8400BAFE2F66443613DC38AE3D9D6239"
cookie "JSESSIONID[Expires]" contains "Wed, 13 Jan 2021"
cookie "JSESSIONID[Secure]" exists
cookie "JSESSIONID[HttpOnly]" exists
cookie "JSESSIONID[SameSite]" == "Lax"
```

## 字节测试

```hurl
# Check the SHA-256 response body hash:             
GET https://example.org/data.tar.gz

HTTP 200
[Asserts]
sha256 == hex,039058c6f2c0cb492c533b0a4d14ef77cc0f78abccced5287d84a1a2011cfb81;

# Checking Byte Order Mark (BOM) in Response Body
GET https://example.org/data.bin

HTTP 200
[Asserts]
bytes startsWith hex,efbbbf;
```

## SSL验证

```hurl
# Check attributes of the SSL certificate            
GET https://example.org

HTTP 200
[Asserts]
certificate "Subject" == "CN=example.org"
certificate "Issuer" == "C=US, O=Let's Encrypt, CN=R3"
certificate "Expire-Date" daysAfterNow > 15
certificate "Serial-Number" matches /[\da-f]+/
```

## 重试

```hurl
# Pull job status until it is completed
GET https://api.example.org/jobs/{{job_id}}
[Options]
retry: 10  # maximum number of retry, -1 for unlimited

HTTP 200
[Asserts]
jsonpath "$.state" == "COMPLETED"
```
