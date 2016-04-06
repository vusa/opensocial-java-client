This document discusses several troubleshooting options available if you run into any issues using this library.



# Enable debug output #

This project uses the `java.util.Logging` framework to log all requests and responses. Specifically, the HTTP method and complete request URL and payload (if any) are logged at the FINEST level for every request along with the status code (e.g. 200, 400) and raw JSON returned. This output can be very helpful when debugging a particular issue.

A sample `logging.properties` file is included in `java/src` which pushes these statements to the standard error output stream (console). You can tweak this properties file as needed to redirect log output to a file or any other handler.

If you're building your application in Eclipse, you can enable logging by right-clicking on the Java file that you want to run and selecting Run As -> Run Configurations.... In the "VM arguments" text area under the "Arguments" tab, paste the following:

```
-Djava.util.logging.config.file=<path to logging.properties>
```

Make sure to substitute the path of your logging.properties file with respect to the root directory of your project. Now when you hit "Apply" and run the executable, you should see log output for all requests sent by the library in the "Console" tab.

For other execution environments such as Tomcat, please read the appropriate documentation to determine how to specify the location of your logging.properties file.

# Run the included online tests #

A number of unit tests are available for each of the various services in `java/test`. The online tests which execute live requests against actual containers are located in the `org.opensocial.online` package. You can view the source of each test to understand how the requests are set up, then run the tests to see how the various requests are executed. Make sure to enable logging output as described above to see the responses.

# Read your container's developer documentation #

Not all containers support every OpenSocial REST/RPC operation. For example, orkut doesn't allow activities to be posted via the server-to-server protocols, and MySpace doesn't support AppData at all. If you're seeing an odd response and the debug output doesn't help, look up any developer resources that your container has available to make sure that the operation you're trying to do is supported and, if it is, that you're passing all required parameters.

For links to a number of OpenSocial developer resources, check out [wiki.opensocial.org](http://wiki.opensocial.org/index.php?title=Main_Page).

# Post in the discussion group #

There is a [discussion forum/mailing list](http://groups.google.com/group/opensocial-client-libraries) set up for the OpenSocial client libraries. If you're running into an issue that isn't answered by the debug output or the developer documentation for your container, then please feel free to post a new topic in the discussion group. Make sure to include as much information in your post as possible, including the version of the library that you're using, what container and authentication scheme you're using, which operation you're attempting, and how it's failing. Make sure to include your debug output and a snippet of your source so we can try to reproduce. Don't include your consumer secret -- if this is necessary, then we'll follow up in a private reply.