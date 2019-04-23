详情参考[wiki](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)  

**1xx Informational**  
- 100 Continue
- 101 Switching Protocols
- 102 Processing
- 103 Early Hints  

**2xx Success**
- 200 OK
- 201 Created
- 202 Accepted
- 203 Non-Authoritative Information(since HTTP/1.1)
- 204 No Content
- 205 Reset Content
- 206 Partial Content
- 207 Multi-Status
- 208 Already Reported
- 226 IM Used  

**3xx Redirection**
- 300 Multiple Choices
- 301 Moved Permanently
- 302 Found (Previously "Moved temporarily"), you should use 303 or 307 instead of 302
- 303 See Other (since HTTP/1.1)
- 304 Not Modified
- 305 Use Proxy (since HTTP/1.1)
- 306 Switch Proxy, No longer used
- 307 Temporary Redirect (since HTTP/1.1), parallel the behaviors of 302, but do not allow the HTTP method to change
- 308 Permanent Redirect, parallel the behaviors of 301, but do not allow the HTTP method to change  

**4xx Client errors**  
- 400 Bad Request
- 401 Unauthorized
- 402 Payment Required 
- 403 Forbidden
- 404 Not Found
- 405 Method Not Allowed
- 406 Not Acceptable
- 407 Proxy Authentication Required
- 408 Request Timeout
- 409 Conflict, Indicates that the request could not be processed because of conflict in the current state of the resource
- 410 Gone, Indicates that the resource requested is no longer available and will not be available again
- 411 Length Required
- 412 Precondition Failed
- 413 Payload Too Large
- 414 URI Too Long
- 415 Unsupported Media Type
- 416 Range Not Satisfiable
- 417 Expectation Failed, The server cannot meet the requirements of the Expect request-header field.
- 418 I'm a teapot
- 421 Misdirected Request
- 422 Unprocessable Entity 
- 423 Locked, The resource that is being accessed is locked
- 424 Failed Dependency
- 425 Too Early
- 426 Upgrade Required
- 428 Precondition Required
- 429 Too Many Requests
- 431 Request Header Fields Too Large
- 451 Unavailable For Legal Reasons  

**5xx Server errors**
- 500 Internal Server Error
- 501 Not Implemented
- 502 Bad Gateway
- 503 Service Unavailable
- 504 Gateway Timeout
- 505 HTTP Version Not Supported
- 506 Variant Also Negotiates
- 507 Insufficient Storage 
- 508 Loop Detected
- 510 Not Extended
- 511 Network Authentication Required  

**Unofficial codes**  
- 103 Checkpoint
- 218 This is fine (Apache Web Server)
- 419 Page Expired (Laravel Framework)
- 420 Method Failure (Spring Framework)
- 450 Blocked by Windows Parental Controls (Microsoft)
- 498 Invalid Token (Esri)
- 499 Token Required (Esri)
- 509 Bandwidth Limit Exceeded (Apache Web Server/cPanel)
- 526 Invalid SSL Certificate
- 530 Site is frozen
- 598 (Informal convention) Network read timeout error  

**Internet Information Services**  
- 440 Login Time-out
- 449 Retry With
- 451 Redirect  

**nginx**
- 444 No Response
- 494 Request header too large
- 495 SSL Certificate Error
- 496 SSL Certificate Required
- 497 HTTP Request Sent to HTTPS Port
- 499 Client Closed Request

**Cloudflare**  
- 520 Unknown Error
- 521 Web Server Is Down
- 522 Connection Timed Out
- 523 Origin Is Unreachable
- 524 A Timeout Occurred
- 525 SSL Handshake Failed
- 526 Invalid SSL Certificate
- 527 Railgun Error
- 530 Origin DNS Error
