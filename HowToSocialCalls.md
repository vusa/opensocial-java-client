Once you've [set up a request authorization scheme](HowToConnecting.md) and instantiated a new `org.opensocial.Client`, you can start making calls against the OpenSocial APIs.



# Requests #
All OpenSocial data requests and other calls executed by this library are modeled as `org.opensocial.Request` objects. For example, here's code that creates and sends a request for the current viewer:

```
//...
Request request = PeopleService.getViewer();
client.send(request);
```

# Batching #
Batching requests can help the performance of your application, especially when the targeted container supports the OpenSocial RPC protocol. This library does **not** have special objects to support batching -- instead, you just "put" your requests in a `java.util.Map` keyed off the request ID which you designate yourself:

```
//...
Map<String, Request> requests = new HashMap<String, Request>();
requests.put("viewerFriends", PeopleService.getFriends());
requests.put("viewer", PeopleService.getViewer());
client.send(requests);
```

When batching requests this way, each request must have a unique ID/label which will be used to access the response data for the individual requests later.

# Processing Results #
After sending a batch request, the library will return a `java.util.Map` of `org.opensocial.Response` objects keyed off the request IDs that you specified earlier -- there should be a 1:1 correlation between requests and responses. You can therefore access the response data of a specific operation like this:

```
//...
Map<String, Response> responses = client.send(requests);
Response item = responses.get("viewerFriends");
```

If you'd like to iterate over each result item, then you can do so like this:

```
for (Map.Entry<String, Response> responseEntry : responses.entrySet()) {
  // Process the individual response
}
```

# Handling errors #
This library includes basic exception-handling to catch certain errors, such as when an HTTP connection fails or an unsupported operation is attempted. One of two exceptions may be thrown: `org.opensocial.RequestException` indicating a failure in sending or authorizing the request or parsing the container's response; or `java.io.IOException` when an error occurs while transmitting the request. The exception message should hopefully provide a clear indication of why the exception was thrown:

```
//...
try {
  client.send(request);
} catch (RequestException e) {
  System.out.println("RequestException thrown: " + e.getMessage());
} catch (IOException e) {
  System.out.println("IOException thrown: " + e.getMessage());
}
```