To use the client library, you must first configure it to connect to an OpenSocial-compatible container.  To do this, you need to create two objects:

  * An `org.opensocial.providers.Provider` instance: contains configuration information for a particular container.
  * An `org.opensocial.auth.AuthScheme` implementation: specifies how OpenSocial requests should be authorized for the specified container.

With these objects, you will be able to instantiate an `org.opensocial.Client`, which is used to make OpenSocial calls against the specified container.





# Creating an `org.opensocial.providers.Provider` instance #
The `Provider` class contains configuration information for a particular OpenSocial container. Many container configurations are already available as subclasses of `Provider`, but you can also create custom subclasses if necessary. It is recommended that you use the pre-configured providers when possible for convenience and consistency.

## Using a pre-configured provider ##

Several container configurations ship with the client library:

| **iGoogle, Gmail** | `new GoogleProvider()` |
|:-------------------|:-----------------------|
| **Google Friend Connect** | `new FriendConnectProvider()` |
| **Hi5**            | `new Hi5Provider()`    |
| **MySpace**        | `new MySpaceProvider()` |
| **orkut**          | `new OrkutProvider()`  |
| **orkut sandbox**  | `new OrkutSandboxProvider()` |
| **Partuza**        | `new PartuzaProvider()` |
| **Plaxo**          | `new PlaxoProvider()`  |
| **Shindig (localhost)** | `new ShindigProvider()` |
| **Yahoo!**         | `new YahooProvider()`  |

Several containers, including orkut and iGoogle, support both REST and RPC protocols. If supported, the RPC protocol is always favored because it supports request batching, but in the event that you wish to force the REST endpoint to be used instead, there should be an alternate constructor available which accepts a `boolean`. See the Javadocs for more information.

## Manually configuring a provider ##

If you are writing code for a container which is not pre-configured, then just create an instance of the `org.opensocial.providers.Provider` directly:

```
Provider provider = new Provider();
provider.setName("ExampleContainer");
provider.setRestEndpoint("http://api.example.com/");
//...
```

The parameters needed are:

|name| `String` | A name identifying this provider. |
|:---|:---------|:----------------------------------|
|version| `String` | The latest OpenSocial version supported by the container; defaults to "0.8". |
|rpcEndpoint| `String` | The base URL of the container's OpenSocial RPC protocol implementation. |
|restEndpoint| `String` | The base URL of the container's OpenSocial REST protocol implementation. |
|requestTokenUrl| `String` | The OAuth URL used to obtain an unauthorized Request Token. |
|authorizeUrl| `String` | The OAuth URL used to obtain user authorization for Consumer access. |
|accessTokenUrl| `String` | The OAuth URL used to exchange the user-authorized Request Token for an Access Token. |
|requestTokenParameters| `Map`    | Extra OAuth parameters (if any) required by the provider to process the Request Token request. |
|signBodyHash| `boolean` | Indicates whether the request body (if any) should be signed along with the request URL; defaults to `true`. |


# Creating an `org.opensocial.auth.AuthScheme` instance #

## 2-legged OAuth ##
2-legged OAuth authorizes your Java application to access the information of any user who has installed a corresponding gadget/widget (sharing the same consumer key) on the container you are connecting to. This style of authorization is ideal for performing background processing on behalf of a gadget-based application installed/running within the container.

The `AuthScheme` implementation you need in order to use 2-legged OAuth is `OAuth2LeggedScheme`. Before you can use this class, you will first need to obtain the **consumer key** and **consumer secret** values for your application. You will usually be able to obtain these values when registering your application or the corresponding gadget/widget with the container. For Google's OpenSocial containers (_orkut_, _iGoogle_, _Gmail_) you will need to fill out this form to obtain a 2-legged key for your application: https://www.google.com/gadgets/directory/verify

Once you have these values, use them to create an `OAuth2LeggedScheme` object:
```
AuthScheme scheme = new OAuth2LeggedScheme("<consumer key>", "<consumer secret>");
```

If you need to make 2-legged requests on behalf of a user (for example, updating AppData for a specific user) OR if you want to execute data requests which reference the "@me" token (i.e. all viewer requests including `PeopleService.getViewer()` and `PeopleService.getFriends()`), you will need to pass that user's OpenSocial ID into the `OAuth2LeggedScheme` constructor:

```
AuthScheme scheme = new OAuth2LeggedScheme("<consumer key>", "<consumer secret>", "<user ID>");
```

## 3-legged OAuth ##
3-legged OAuth allows users who have not installed your application on a social network to explicitly grant your application permission to access their data. This style of authorization is best for websites or web/mobile/desktop applications which want to work with social data but do not run applications inside of social networks.

The `AuthScheme` implementation you need in order to use 3-legged OAuth is `OAuth3LeggedScheme`. Before you can use this class, you will first need to obtain the **consumer key** and **consumer secret** values for your application as described in the 2-legged OAuth section. You will also need an instance of a `Provider` class:

```
AuthScheme scheme = new OAuth3LeggedScheme(new GoogleProvider(), "<consumer key>", "<consumer secret>");
```

To kick off the 3-legged "dance", you first call the `getAuthorizationUrl` instance method, passing in the URL that you want the user redirected to after he has been authenticated and granted the necessary permissions. Once you have this URL, it is your responsibility to redirect the user to this page. Once the user is redirected back to your application, you must retrieve a reference to the `OAuth3LeggedScheme` instance (it's easiest to store the instance in a session and retrieve it directly; alternatively, you can just persist the request token and recreate the `OAuth3LeggedScheme` instance, but this requires more code -- see the included ThirdLeg demo) and call the `requestAccessToken` instance method. After the flow is complete, you can pass the instance into the `Client` constructor.

## Security Token ##
If you are making REST calls for a gadget running inside of a social network, you can use the gadget's security token to authorize your requests. Once you pass the value of the security token from your gadget to the server backend, use:

```
AuthScheme scheme = new SecurityTokenScheme("<security token>");
```

The default token parameter name is "st". There is an alternative constructor which enables you to pass a custom security token parameter name.

## FCAuth Token ##
Google Friend Connect has support for using a [site authentication cookie](http://code.google.com/apis/friendconnect/opensocial_rest_rpc.html#site-cookie) to authorize your client library requests. This works in the same way as the security token, but needs to be passed to the RPC or REST endpoint as a parameter named `fcauth`.  Configure your client in the following manner to use this parameter:

```
AuthScheme scheme = new FCAuthScheme("<fcauth token>");
```

The FCAuth token value is sent as a cookie to your server when a user is logged in. From a JavaEE environment, you should be able to access the value of this token by checking for a cookie named `fcauth<site ID number>`:

```
String siteId = "<Friend Connect site ID>";
String token = null;

for (Cookie cookie : request.getCookies()) {
  if (cookie.getName().equals("fcauth" + siteId)) {
    token = cookie.getValue();
    break;
  }
}
```


# Creating an `org.opensocial.Client` instance #
When you have obtained both an `AuthScheme` and `Provider` instance as described above, pass them to the `Client` constructor to get an object you can use to make social data requests:

```
Client client = new Client(provider, scheme);
```

# Examples #
Below are examples for configuring the library.

## 2-legged OAuth on orkut ##
```
Provider provider = new OrkutProvider();
AuthScheme scheme = new OAuth2LeggedScheme("<consumer key>", "<consumer secret>", "<OpenSocial ID>");

Client client = new Client(provider, scheme);
```

## 3-legged OAuth on iGoogle ##
See the included App Engine-based demo application called ThirdEye (in `java/demos`) which includes a `Filter` that demonstrates how to do the 3-legged OAuth dance using this library.