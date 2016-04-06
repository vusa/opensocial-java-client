This page discusses how to request and work with people data from an OpenSocial container.



# Fetching #

## Creating the request ##

To retrieve a user or group of users, use the static methods provided by the `org.opensocial.services.PeopleService` class.

For example, to fetch profile information for the application's _viewer_ (current user), use:

```
Request request = PeopleService.getViewer();
```

To fetch profile information for a specific user, you must know the OpenSocial ID of that user:

```
Request request = PeopleService.getUser("<user's OpenSocial ID>");
```

To fetch a list of friends, call `getFriends`. There are two such methods available, one accepting 0 arguments, the other accepting an OpenSocial ID. This is a convention repeated in most services -- the zero-argument call implictly targets the application's _viewer_ (current user) while a specific user can be targeted using the second call:

```
// Request the viewer's friends
Request viewerFriends = PeopleService.getFriends();

// Request a specific user's friends
Request userFriends = PeopleService.getFriends("<user's OpenSocial ID>");
```

Once the request is created, you can call several methods on it to tune your request, e.g. to set the number of friends or specific profile fields returned. These `Request` methods are summarized below.

| **Method** | **Accepts** | **Description** |
|:-----------|:------------|:----------------|
| `setCountParameter` | `int`       | The maximum number of results to return. |
| `setStartIndexParameter` | `int`       | The starting index of the results to return (for paging). |
| `setFieldsParameter` | `String[]`  | Array of [profile fields](http://www.opensocial.org/Technical-Resources/opensocial-spec-v09/REST-API.html#personFields) to fetch.  You may set this to **"@all"** to fetch all available fields. |

Additional parameters you can use are listed in the [OpenSocial specification](http://www.opensocial.org/Technical-Resources/opensocial-spec-v09/REST-API.html#standardQueryParameters) and can be set via a `Request` object's `addParameter` or `addParameters` methods.

## Response ##

After executing a request, a `Client` will return a `Response` object encapsulating all information returned by the container including the requested data. For brevity, I will mention only how to retrieve the requested data, i.e. profile information for a user or group of users. The other `Response` methods should be self-explanatory and Javadocs are available for them.

The library attempts to parse the data returned by the library as one or more `org.opensocial.models.Model` (or `org.opensocial.models.Model` subclass) objects, and makes this available to client applications via the `getEntry` and `getEntries` methods. Some responses may not have any entries, others may have a single entry, and still others can have many entries.

Responses for a successful **@self** (viewer or user) request should have a single entry, namely the profile information for the requested user. This data can be retrieved as an `org.opensocial.models.Person` object.

```
//...
Request request = PeopleService.getViewer();
Response response = client.send(request);

Person viewer = response.getEntry();
```

`Person` extends `Model` so all model methods are available to it as well as convenience methods for getting the ID, name, and thumbnail URL of the returned user. For any `Model` object, you can get the full set of fields as a `String[]` by calling the `getFieldNames` instance method.

Responses for a successful friends request can have many entries or none, depending on the number of friends of the specified user. For cases where the number of response entries can be many, you should call the `Response` object's `getEntries` method which returns a `java.util.List`:

```
//...
Request request = PeopleService.getFriends();
Response response = client.send(request);

List<Person> friends = response.getEntries();
for (Person friend : friends) {
  // Process an individual friend
}
```