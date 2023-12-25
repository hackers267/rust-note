# Hurl请求示例

## 处理请求头

```hurl
# A simple GET with headers            
GET https://example.org/news
User-Agent: Mozilla/5.0 
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
```

## 链式请求

```hurl
# Chaining requests is easy, responses are optional
GET https://example.org/api/health

GET https://example.org/api/step1
HTTP 200

GET https://example.org/api/step2

GET https://example.org/api/step3
```

## 请求参数

```hurl
# Use query params in the URL:
GET https://example.org/news?order=newest&search=something%20to%20search&count=100


# Or with a query param section:            
GET https://example.org/news
[QueryStringParams]
order: newest
search: something to search
count: 100
```

## 基础验证

```hurl
GET https://example.org/protected
[BasicAuth]
bob: secret


# One can also use an Authorization header
# Authorization header value can be computed with `echo -n 'bob:secret' | base64`
GET https://example.org/protected
Authorization: Basic Ym9iOnNlY3JldA==
```

## 表单

```hurl
POST https://example.org/contact
[FormParams]
default: false
token: {{token}}
email: john.doe@rookie.org
number: 33611223344
```

## 文件上传

```hurl
POST https://example.org/upload
[MultipartFormData]
field1: value1
field2: file,example.txt;
# One can specify the file content type:
field3: file,example.zip; application/zip
```

## Cookies

```hurl
# Requests in the same Hurl file share the cookie storage
# Cookies can also be set per request    
GET http://localhost:8000/cookies/set-multiple-request-cookies
[Cookies]
user1: Bob
user2: Bill
user3: {{name}}
HTTP 200


# Or we can simply use a Cookie header
GET https://example.org/index.html
Cookie: theme=light; sessionToken=abc123
```

## 捕获数据

```hurl
# Get home:
GET https://example.org

HTTP 200
[Captures]
csrf_token: xpath "string(//meta[@name='_csrf_token']/@content)"


# Do login!
POST https://example.org/login?user=toto&password=1234
X-CSRF-TOKEN: {{csrf_token}}

HTTP 302
```

## Json请求体

```hurl
# Create a new doggy thing with JSON body:
POST https://example.org/api/dogs
{
    "id": 0,
    "name": "Frieda",
    "picture": "images/scottish-terrier.jpeg",
    "age": 3,
    "breed": "Scottish Terrier",
    "location": "Lisco, Alabama"
}
```

## SOAP/XML 请求体

```hurl
POST https://example.org/InStock
Content-Type: application/soap+xml; charset=utf-8
SOAPAction: "http://www.w3.org/2003/05/soap-envelope"
<?xml version="1.0" encoding="UTF-8"?>
<soap:Envelope xmlns:soap="http://www.w3.org/2003/05/soap-envelope" xmlns:m="http://example.net">
  <soap:Header></soap:Header>
  <soap:Body>
    <m:GetStockPrice>
      <m:StockName>GOOG</m:StockName>
    </m:GetStockPrice>
  </soap:Body>
</soap:Envelope>
```

## GraphQL

````hurl

# GraphQL queries are supported, even with variables            
POST https://example.org/starwars/graphql
```graphql
{
  human(id: "1000") {
    name
    appearsIn
    height(unit: FOOT)
  }
}
```
````

## 文本请求体

````hurl
POST https://example.org/models
Content-Type: text/csv
```
Year,Make,Model,Description,Price
1997,Ford,E350,"ac, abs, moon",3000.00
1999,Chevy,"Venture ""Extended Edition""","",4900.00
1999,Chevy,"Venture ""Extended Edition, Very Large""",,5000.00
1996,Jeep,Grand Cherokee,"MUST SELL! air, moon roof, loaded",4799.00
```
````

## 二进制请求体

```hurl
POST https://example.org
# Some random comments before body
base64,TG9yZW0gaXBzdW0gZG9sb3Igc2l0IGFtZXQsIGNvbnNlY3RldHVyIG
FkaXBpc2NpbmcgZWxpdC4gSW4gbWFsZXN1YWRhLCBuaXNsIHZlbCBkaWN0dW0g
aGVuZHJlcml0LCBlc3QganVzdG8gYmliZW5kdW0gbWV0dXMsIG5lYyBydXRydW
0gdG9ydG9yIG1hc3NhIGlkIG1ldHVzLiA=;

# Or use a file
POST https://example.org
file,data.bin;
```

