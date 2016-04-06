This client library helps you work with social network data from your web server. This document will help explain where you can obtain the files for the client library and how to use them in your own projects.



# Obtaining the client library #

There are two options for obtaining the files for the client library.

## Obtaining the pre-packaged release ##
Most developers will want to use a pre-packaged release, which are typically the most stable versions of the library.

Click on the [Downloads tab](http://code.google.com/p/opensocial-java-client/downloads/list) to download the library. There are two downloads listed: a zipped archive of the entire project including dependencies, documentation, unit tests, and samples as well as a Java Archive of the standalone library (most useful when you already have the dependencies on your system). For reference, the Java client library depends on [json\_simple-1.1.jar](http://opensocial-java-client.googlecode.com/svn/trunk/java/lib/json_simple-1.1.jar), [commons-codec-1.3.jar](http://opensocial-java-client.googlecode.com/svn/trunk/java/lib/commons-codec-1.3.jar), and [oauth-20090825.jar](http://opensocial-java-client.googlecode.com/svn/trunk/java/lib/oauth-20090825.jar). Additionally, [junit-4.5.jar](http://opensocial-java-client.googlecode.com/svn/trunk/java/lib/junit-4.5.jar) is required for compiling and running the included unit tests.

After extracting the library from the zipped archive, you will have a new `opensocial-java-client` directory. The source files for the library can be found in `opensocial-java-client/java/src`.

## Obtaining the most up-to-date version from SVN ##
The most up-to-date version of the code is available from this project's SVN repository. Obtaining the code via SVN enables developers to get early access to fixes or features that have not yet been released in the pre-packaged version or for developers who want to contribute patches back to the project. Note that SVN releases are not guaranteed to be stable -- the code may be buggy and certain interfaces may change before the next release.

Obtain the code by using the following SVN checkout command (you will need an SVN client installed on your computer):
```
  svn checkout http://opensocial-java-client.googlecode.com/svn/trunk/ opensocial-java-client
```

# What to do with the files #

If you downloaded the Java Archive earlier, you won't have to worry about building the library yourself. All you need to do is add `opensocial.jar` and the dependencies listed above to your Classpath (or your Web application's lib directory) and you're set. If you downloaded the zipped archive, you will need to build the library first before you can use it. Fortunately, this is very easy -- just follow the instructions in the included [README](http://opensocial-java-client.googlecode.com/svn/trunk/README) file. The archive will be generated in the `dist` directory which you can then copy (along with the dependencies in `java/lib`) to your project.

To be able to effectively use the library, you must add it to your Classpath. If you're running a command-line application, specify the location of the libraries via the `-classpath` option of the `java` executable. If you're instead planning to integrate these libraries into a Java-based Web application, you can place these libraries in your project's `war/WEB-INF/lib` directory. The Java application server typically adds any JARs in this directory to your Classpath automatically. Your application server may also have a directory setup for sharing dependencies across projects which saves you the trouble of constantly re-copying these JARs every time you create a new OpenSocial project.

Once your Classpath is set (and you've restarted your application server if applicable), you're all ready to use the library! The Java library is composed of various classes, which you have to import into your own classes/servlets. For example, before you can instantiate a new `Client`, import the class:

```
  import org.opensocial.Client;
```