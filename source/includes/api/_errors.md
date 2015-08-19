## Errors

The NimbleCommerce API uses HTTP response codes to handle requests results for most of API calls. API uses the following HTTP response codes to indicate request execution result.

Error Code | Meaning
---------- | -------
400 | Bad Request -- The request could not be understood by the server due to malformed syntax.
401 | Unauthorized -- The request requires user authentication. The response MUST include a Authorization header field with valid authorization token.
403 | Forbidden -- The request was a legal request, but the server is refusing to respond to it. Error code for user not authorized to perform the operation or the resource is unavailable for some reason.
404 | Not Found -- The server has not found anything matching the Request-URI.
405 | Method Not Allowed -- The method specified in the Request-Line is not allowed for the resource identified by the Request-URI.
406 | Not Acceptable -- The resource identified by the request is only capable of generating response entities which have content characteristics not acceptable according to the accept headers sent in the request.
410 | Gone -- The requested resource is no longer available at the server and no forwarding address is known.
500 | Internal Server Error -- We had a problem with our server. Try again later.
502 | Bad Gateway -- The server was acting as a gateway or proxy and received an invalid response from the upstream server.
503 | Service Unavailable -- We're temporarially offline for maintanance. Please try again later.

<aside class="notice">
However there are a number of outdated API methods that return result in `status` field of response json. Generally this methods will return status 200 to indicate success and 500 to indicate that error happened.
</aside>

