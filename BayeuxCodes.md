# Introduction #

List of Bayeux error/response codes.  Includes constant, closest matching HTTP response code, and response text.


# Details #

The error field will take the form of:  (from protocol doc)

```
error            = error_code ":" error_args ":" error_message 
                 | error_code ":" ":" error_message
error_code       = digit digit digit
error_args       = string * "," string )
error_message    = string
```


Example:
```
[
  {
     "channel": "/meta/invalidchannel",
     "successful": false,
     "error": "405:/meta/invalidchannel:The meta channel specified is not valid.",
     "clientId": "Un1q31d3nt1f13r"
   }
]
```


List of error codes is below:


| **code** | **constant** | **message** | **explanation** |
|:---------|:-------------|:------------|:----------------|
|300|RC\_VERSION\_MISMATCH|Version number mismatch.|Status code 300, version mismatch, is used when an incoming handshake request contains a version the server does not support.  The server should respond with version and minimum version values in failed handshake response.  The client should rehandshake only if it can support a version advertised by the server.  The advice field indicates if client should retry the handshake.  The error code provides additional information on why handshake failed.|
|301|RC\_CONNTYPE\_MISMATCH|Connection type mismatch.|Status code 301, connection type mismatch, is used when an incoming handshake request's supportedConnectionTypes field does not match a supported connection type on the server.  The server should respond with list of supportedConnectionTypes in the response.  The client should re-handshake only if it provides a supportedConnectionType advertised by the server.The advice field indicates if client should retry the handshake.  The error code provides additional information on why handshake failed.|
|302|RC\_EXT\_MISMATCH|Extension mismatch.|Status code 302, extension mismatch, is used when an incoming handshake request's  extension field does not match a supported extension on the server.  The server should respond with list of supported extensions in the response.  The client should re-handshake only if it provides a valid (or no) extension field advertised by the server.  The advice field indicates if client should retry the handshake.  The error code provides additional information on why handshake failed.|
|400|RC\_BAD\_REQUEST|The incoming request could not be recognized by the server.| Status code 400, bad request, is used when an incoming request is not recognized by the server.   A typical case would be when the request contains invalid JSON.|
|401|RC\_CLIENT\_UNKNOWN|The client ID specified is unknown.|Response code 401, client unknown, is used when an incoming request contains a client ID the server does not recognized.|
|402|RC\_PARAMETER\_MISSING|The incoming request is missing a required parameter.|Response code 402, parameter missing, is used when an incoming request is missing a required parameter.  The argument list may contain the missing parameter name.|
|403|RC\_CHANNEL\_FORBIDDEN|Access to the specified channel is forbidden.|Response code 403, channel forbidden, is used to indicate the client is not authorized access to the request channel.   Meta requests that could use this response code include /meta/subscribe and a Bayeux publish request. |
|404|RC\_CHANNEL\_UNKNOWN|The channel specified is unknown.|Response code 404, channel unknown, is the response code used when a  subscribe, unsubscribe, or publish request tries to access a channel that does not exist.|
|405|RC\_CHANNEL\_INVALID|The channel specified is not valid.|Response code 405, channel invalid, is used when the channel format in a request is not valid or for a request to an unknown reserved meta channel.|
|406|RC\_EXT\_UNKNOWN|The extension specified is unknown.|Response code 406, extension unknown, is used when the incoming bayeux request message contains an ext field that the server does not recognize.|
|407|RC\_PUBLISH\_FAILED|The publish request has failed.|Response code 407, publish failed, is used when the server is unable or unwilling to publish the data provided by the client.  Examples could include a server filtering content, or a server limited the amount of publish requests by a client.   Response code 403 should be used when a client is not authorized to publish to a channel.|
|500|RC\_SERVER\_ERROR|Server error.|Response code 500, server error, is used when there was an internal server error completing the request.  The advice field will specify if the client should rehandshake, retry, reconnect, or not reconnect.|























