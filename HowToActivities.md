This page discusses how to request, create, and work with OpenSocial activity data.



# Fetching #

## Creating the request ##

To fetch the activities posted by a user or the friends of a user, use the static methods provided by the `org.opensocial.services.ActivitiesService` class.

For example, to fetch all activities posted by your application for the current user (_viewer_) or for a specific user whose OpenSocial ID is known:

```
// Request the viewer's activities
Request viewerActivities = ActivitiesService.getActivities();

// Request a specific user's activities
Request userActivities = ActivitiesService.getActivities("<user's OpenSocial ID>");
```

You can also fetch activities posted by your application for friends of a specific user:

```
// Pass "@me" to request the viewer's friends' activities specifically
Request userFriendsActivities = ActivitiesService.getFriendActivities("<user's OpenSocial ID>");
```

As with people requests, you can call several methods on the `Request` object(s) returned in order to tune your request:

| **Method** | **Accepts** | **Description** |
|:-----------|:------------|:----------------|
| `setCountParameter` | `int`       | The maximum number of results to return. |

Additional parameters you can use are listed in the [OpenSocial specification](http://www.opensocial.org/Technical-Resources/opensocial-spec-v09/REST-API.html#standardQueryParameters) and can be set via a `Request` object's `addParameter` or `addParameters` methods.

## Response ##

After executing a request, a `Client` will return a `Response` object encapsulating all information returned by the container including the requested data. For brevity, I will mention only how to retrieve the requested data, e.g. activities for a user or group of users. The other `Response` methods should be self-explanatory and Javadocs are available for them.

As discussed in a previous article on [fetching people](HowToPeople.md), the requested data is made available to client applications as `org.opensocial.models.Model` objects via the `getEntry` and `getEntries` methods. Specifically for the activity requests discussed above, the fetched activities can be accessed as a `java.util.List` of `org.opensocial.models.Activity` objects like so:

```
//...
Request request = ActivitiesService.getActivities();
Response response = client.send(request);

List<Activity> activities = response.getEntries();
for (Activity activity : activities) {
  // Process an individual activity
}
```

The `Activity` class extends `Model` and offers convience methods for retrieving the ID, title, and body fields among others. As with any `Model` object, you can get the full set of fields as a `String[]` by calling the `getFieldNames` instance method and access arbitrary fields by name (`getField`).

# Creating #

## Creating the request ##

To create a new activity, you must use the `createActivity` static method of the `ActivitiesService` class. This method accepts an `Activity` object which should have all desired activity parameters (e.g. title, titleId, body, etc.) set:

```
//...
Activity activity = new Activity();
activity.setTitle("<activity title>");
activity.setBody("<activity body>");

Request request = ActivitiesService.createActivity(activity);
client.send(request);
```

Note that `createActivity` does not accept an OpenSocial user ID -- for now, the library only allows activities to be created for the current user (_viewer_), which most containers enforce as well.

## Response ##

If an exception isn't thrown, this generally means that the activity was posted successfully. The OpenSocial specification doesn't require the container to return the newly posted activity in response, although some containers like MySpace do return useful data such as a status link. If you want this data, you can call `getEntry` on the `Response` object, and interact with the `Model` instance returned.