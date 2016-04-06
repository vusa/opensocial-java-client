# Using Two-legged OAuth  with Java based Web Apps #

## Overview ##
<p>Seamless integration of  online social  data can greatly improve the overall richness and functionality of the site. With OpenSocial's RESTful protocol, it is now possible to easily integrate social data into existing websites using simple api calls through entirely secure means. This tutorial will step through how to fetch social data from an OpenSocial container site using the OpenSocial Java client library where '2-Legged'  OAuth is used to secure the transaction.<br>
<br>
The following specific steps for this tutorial will covered:</p>

<ul>
<blockquote><li><a href='#intro'>An Introduction to the OpenSocial RESTful Client Libraries</a> </li>
<li><a href='#gettingfamiliar'>Getting familiar with the OpenSocial Java client library</a> </li>
<li><a href='#building'>Building a simple JSP based web app to securely fetch online social data</a></li>
<li><a href='#customize'>How to customize the JSP app to fetch your own orkut data</a> </li>
</ul></blockquote>

<h4><strong><a></a>An Introduction to the  OpenSocial RESTful Client Libraries </strong></h4>
<p>As of OpenSocial version 0.8, it contained the RESTful specification which for the first time allowed for direct communication with a website serving as an OpenSocial container. As a result, the definition of an OpenSocial application has evolved past its original gadget based history. To that end, an OpenSocial application can now be constructed via a collection of technologies and in a variety of programming languages. The apps no longer have to be confined within the traditional browsers as desktop or mobile applications can also interact with social data in the same way as gadgets can do with the OpenSocial JavaScript api. </p>
<p>With OpenSocial's public RESTful specification, any programmer can write code that adheres to the spec in order to communicate as a client with an OpenSocial container. As of December 2008, a set OpenSocial REST client libraries were launched on Google's project hosting site and greatly simplifies the task. In addition to Java, they also come in several other languages including  PHP, Ruby, and Python. There is even an Objective-C library for iPhone developers. </p>
<p>The different OpenSocial client libraries are accessible from the following locations:</p>
<ul>
<blockquote><li><a href='http://code.google.com/p/opensocial-java-client/'><a href='http://code.google.com/p/opensocial-java-client/'>http://code.google.com/p/opensocial-java-client/</a></a></li>
<li><a href='http://code.google.com/p/opensocial-python-client/'><a href='http://code.google.com/p/opensocial-python-client/'>http://code.google.com/p/opensocial-python-client/</a></a></li>
<li><a href='http://code.google.com/p/opensocial-ruby-client/'><a href='http://code.google.com/p/opensocial-ruby-client/'>http://code.google.com/p/opensocial-ruby-client/</a></a></li></blockquote>

<blockquote><li><a href='http://code.google.com/p/opensocial-php-client/'><a href='http://code.google.com/p/opensocial-php-client/'>http://code.google.com/p/opensocial-php-client/</a></a> </li>
<li><a href='http://code.google.com/p/opensocial-objc-client/'><a href='http://code.google.com/p/opensocial-objc-client/'>http://code.google.com/p/opensocial-objc-client/</a></a></li>
</ul>
<h4><strong><a></a>Getting familiar with the OpenSocial Java Client Library</strong></h4>
<p>In order to accomplish this tutorial, you'll first need to familiarize yourself with the OpenSocial Java client library. As mentioned ealier, it is possible to write your own client code to interact with an OpenSocial server as long as it adheres to the OpenSocial RESTful specification, however the published client libraries are open source and freely available so you can easily make use of these.</p>
<p><strong>Downloading and building the Java Client library</strong></p>
<p>Before integrating OpenSocial data via REST, the first task you'll want to do is download and build your own jar files for OpenSocial Java client library. This is done by:</p>
<ol></blockquote>

<blockquote><li>Get the code by navigating to: <a href='http://code.google.com/p/opensocial-java-client/source/checkout'><a href='http://code.google.com/p/opensocial-java-client/source/checkout'>http://code.google.com/p/opensocial-java-client/source/checkout</a></a>
</blockquote><blockquote>and following the instructions for checking out the code.<ul><li>Note: You'll need a subversion client in order to check the code.</li>
<blockquote><li>Incidentally you'll notice that there is a downloadable zip file  available on the site, but this may not contain the latest changes to the client library, so checking out the code with a subversion client is recommended.</li>
</blockquote><blockquote></ul>
</blockquote><blockquote></li>
<li>Once you have the OpenSocial java client library source code, you should be able to build the code by following the instructions in the opensocial-java-client/README-src.txt file.  You'll need to execute the command: 'ant dist'. This will build all of the OpenSocial class files and place the OpenSocial client jar file (opensocial.jar) into a new directory named 'dist'. </li></blockquote></blockquote>

</ol>
<p><strong>Using the OpenSocial Java client library in Java applications </strong></p>
<p>As you noticed, we stepped through how to build the core opensocial.jar file, but in order for this code to work correctly, it will also need several other jar files present in the classpath. Fortunately these files are also provided for you in a separate 'lib' directory. They are: commons-codec.jar, !javax.servlet[...].jar, json.jar, !junit[...].jar and !oauth-core[...].jar. </p>
<p>When you wish to use these to enable your Java application to use the OpenSocial client code, you'll need to make sure that both the core opensocial.jar file as well as the dependent jar files (commons, javax.servlet, json, etc) are also in the classpath. </p>
<h4><a></a>Building a simple JSP based web app to securely fetch online social data</h4>
<p>To enable  a Java EE Web application to be able to be an OpenSocial client, you simply have to place both the opensocial.jar file along with the other required jar files into the webapp's WEB-INF/lib directory. Once added, you can immediately begin using the OpenSocial client code in your Web applications. </p>
<p><strong>A note on the opensocial-java-client/samples </strong></p>
<p>Before jumping into creating a Java EE !Webapp  that uses the OpenSocial client library, you may have noticed that there are already a set of samples provided by the OpenSocial Java client library. These are located in the directory, opensocial-java-client/java/samples. These are mostly straight Java examples of how to use the client library but there are some JSP examples which demonstrate how to connect to an OpenSocial container(server) using 3-legged OAuth specifically. If you're just getting started however, using 2-legged OAuth is easy enough to use. For this tutorial, we'll start by converting over the simple DisplayFriends.java example into a simple Web app version.</p>
<p><strong>Preparation building your first Java WebApp </strong></p>

<p>This tutorial makes use of a Java WebApp running on a &quot;JSP/Servlet container&quot;. For this tutorial, <a href='http://tomcat.apache.org/'>Apache Tomcat</a> is used, but other Java containers such as Glassfish, and Weblogic should work. If you already have experience building Java EE Web apps, you can easily skip ahead.</p>
<p>Building a Java !Webapp usually requires at least 2 files in a defined directory structure. For this example, we'll assume you are building a webapp for the Tomcat server. </p>
<p>Starting in the &lt;tomcat_home/webapps directory of Tomcat, create a subdirectory, 'twolegged'. This directory will contain your new application. </p>
<p>Now cd into the this directory and create a subdirectory, 'WEB-INF'. </p>
<p>Change into this new directory and create a file named web.xml and place the following content into it.</p>

```
<?xml version = '1.0' encoding = 'ISO-8859-1'?>
   <web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd" version="2.4" xmlns="http://java.sun.com/xml/ns/j2ee">
 <description>Empty web.xml file for Java EE Web Application</description>
 </web-app>
```

<p><strong>Adding the OpenSocial jar files to your webapp </strong></p>
<p>Now that you've added the required web.xml file, you'll need to add the OpenSocial Java client jar files to the webapp. This is done by creating a subdirectory, 'lib', under the WEB-INF directory. Once you have added your lib directory under your WEB-INF directory, you can then copy the necessary OpenSocial and required complementary jar files:</p>
<ul>
<blockquote><li>opensocial.jar</li>
<li>oauth-core-20090105.jar</li>
<li>junit-4.5.jar</li>
<li>json.jar</li>
<li>javax.servlet_2.4.0.v200806031604.jar</li>
<li>commons-codec-1.3.jar </li>
</ul>
<p>Note: The required jar files for OAuth, JUnit, JSON etc, listed here are the versions that are associated with the OpenSocial Java Client libraries at the time of writing of this article (July 2009).</p>
<p>Now that you have the pre-requisite files for your web app to communicate with an OpenSocial container with 2-legged, OAuth you can now build a JSP to access data from any OpenSocial container supporting 2 or 3 legged OAuth. For the remainder of this tutorial, we'll focus on communicating with orkut, using 2-legged OAuth.</p>
<p><strong>Creating a JSP that displays friend and profile data from orkut  </strong></p>
<p>Before making a JSP page that displays profile and friend data from orkut, we'll start by creating a simple starter JSP page named 'profile_friends.jsp' and place it in the top level directory. You can use the following starter code in your JSP page. </p></blockquote>

```
<html>
  <head>
    <title>List Friends from orkut using 2-Legged OAuth</title>
  </head>
  <body>
    <h2>List Friends from orkut using 2-Legged OAuth</h2>

    <h3>Profile Information:</h3>

    <h3>Your Friends are:</h3>

  </body>
</html>
```

<p>Your directory structure should now be as follows:</p>
<p><img src='http://ffch5eaa.joyent.us/ostutorials/twoleggedjava/directory-structure-webapp1.jpg' alt='dir-struc' width='339' height='200' /></p>
<p>At this point you may want to doublecheck that your JSP page renders correctly in your Java EE container by starting up your container (Tomcat for this example) and accessing the url: </p>
<p><a href='http://localhost:8080/twolegged/profile_friends.jsp'>http://localhost:8080/twolegged/profile_friends.jsp</a> </p>
<p>If everything is correct so far, you should be able to see your starter JSP page rendered in the browser. </p>
<p>Now we can begin adding the necessary code to enable your JSP page to make contact to orkut. </p>
<p>To being with, add the following code to your JSP page, directly before the first < html > tag:</p>

```
<%@ page import="java.util.Collection" %>
<%@ page import="org.opensocial.data.OpenSocialPerson" %>
<%@ page import="org.opensocial.client.OpenSocialClient" %>
<%@ page import="org.opensocial.client.OpenSocialProvider" %>

<%! String consumerKey = "orkut.com:623061448914"; %>
<%! String consumerSecret = "uynAeXiWTisflWX99KU1D2q5"; %>
<%! String viewerId = "03067092798963641994"; %>
<%! String providerName = "ORKUT_SANDBOX"; %>

<%
   final OpenSocialProvider provider = OpenSocialProvider.valueOf(providerName);
   final OpenSocialClient client = new OpenSocialClient(provider);
   client.setProperty(OpenSocialClient.Property.CONSUMER_SECRET, consumerSecret);
   client.setProperty(OpenSocialClient.Property.CONSUMER_KEY, consumerKey);
   client.setProperty(OpenSocialClient.Property.VIEWER_ID, viewerId);
   client.setProperty(OpenSocialClient.Property.DEBUG, "true");

   OpenSocialPerson person = client.fetchPerson();

   Collection<OpenSocialPerson> friends =  client.fetchFriends(viewerId);
%>
<html>
...
```

<p>As you can  see in the code, we first import the required Java packages for making OpenSocial client calls. We then set the OAuth consumer key and consumer secret as well as the OpenSocial viewerid and the provider, which for this example is orkut. The final segment of code, creates a new OpenSocialClient based on the provider value (which we specified as orkut) and then sets the client properties for the OAuth parameters as well as the OpenSocial viewerid.</p>
<p>You'll also notice that we set the client property DEBUG, to 'true'. This is an extremely useful option that allows you to see the actual communication stream between the client and the OpenSocial container. </p>

<p>Once all the client properties are set, two calls are made to the OpenSocial container. The first is to fetch the profile information of the person specified by viewerId and the second call is to fetch a collection of friends associated with the same person associated with the specified viewerId. Once these calls are successfully made, their responses are stored in the Java objects: person, and friends and can be displayed in the body of the JSP.</p>
<p><strong>Displaying OpenSocial Profile Data in a JSP </strong></p>
<p>To display the fetched profile information from the fetchPerson call, add the following code to the JSP page directly after the < h3 > Profile Information < /h3 > line:</p>
```
<h3>Profile Information</h3>
<table border="1">
  <tr>
    <td> Your Display Name is: </td><td><%= person.getDisplayName() %></td>
  </tr>
  <tr>
    <td> Your Id is: </td><td><%= person.getId() %></td>
  </tr>
  <tr>
    <td> Your Thumbnail photo is: </td><td><img src="<%= person.getThumbnailUrl() %>"/></td>
  </tr>
</table> 
```


<p>You should now be able to refresh the JSP page in the browser and see the profile information (displayName, Id, and thumbnailUrl) rendered in the HTML table.<br />
<blockquote><br />
Note: Recall again that we've literally taken the same example code along with its sample viewerid and OAuth application consumer key and secret for the OpenSocial Java client library examples: DisplayProfileData.java, and DisplayFriends.java. (This is why we are seeing the profile information for 'API DWH'.) </p></blockquote>

<p><strong>Displaying OpenSocial Friends data in a JSP </strong></p>
<p>You may have noticed already in the code that was added to the JSP also fetched the viewer's friends and placed it in a Collection object named 'friends' of type OpenSocialPerson. Now we simply have to add the necessary code the JSP page to display the fetched friend data.</p>
<p>Add the following code your JSP page directly after the line: < h3 > Your Friends are:< /h3 ></p>

```
 <h3>Your Friends are:</h3>
   <table border="1">
     <%
       for (OpenSocialPerson friend : friends) {
     %>
     <tr>
       <td>Id: <%= friend.getId() %></td>
       <td>
        <center>
          <img src="<%= friend.getThumbnailUrl() %>"/> <br/>
          <%= friend.getDisplayName() %>
        </center>
       </td>
     </tr>
    <%
      }
    %>
  </table>
```

<p>The preceding code simply displays the friends data in an HTML table.</p>
<p>You should be able to click refresh on your browser and see the following page:</p>
<p><img src='http://ffch5eaa.joyent.us/ostutorials/twoleggedjava/list-friends.jpg' alt='list friends' width='500' height='611' />  </p>

<p>Incidentally, the final source of the JSP page is as follows:</p>

```
<%@ page import="java.util.Collection" %>
<%@ page import="org.opensocial.data.OpenSocialPerson" %>
<%@ page import="org.opensocial.client.OpenSocialClient" %>
<%@ page import="org.opensocial.client.OpenSocialProvider" %>
<%! String consumerKey = "orkut.com:623061448914"; %>
<%! String consumerSecret = "uynAeXiWTisflWX99KU1D2q5"; %>
<%! String viewerId = "03067092798963641994"; %>
<%! String providerName = "ORKUT_SANDBOX"; %>
<%
final OpenSocialProvider provider = OpenSocialProvider.valueOf(providerName);
final OpenSocialClient client = new OpenSocialClient(provider);
client.setProperty(OpenSocialClient.Property.CONSUMER_SECRET, consumerSecret);
client.setProperty(OpenSocialClient.Property.CONSUMER_KEY, consumerKey);
client.setProperty(OpenSocialClient.Property.VIEWER_ID, viewerId);
client.setProperty(OpenSocialClient.Property.DEBUG, "true");
OpenSocialPerson person = client.fetchPerson();
Collection<OpenSocialPerson> friends =  client.fetchFriends(viewerId);
%>
<html>
  <head>
    <title>List Friends from orkut using 2-Legged OAuth</title>
  </head>
  <body>
    <h2>List Friends from orkut using 2-Legged OAuth</h2>
    <h3>Profile Information:</h3>
    <table border="1">
      <tr>
        <td> Your Display Name is: </td><td><%= person.getDisplayName() %></td>
      </tr>
      <tr>
        <td> Your Id is: </td><td><%= person.getId() %></td>
      </tr>
      <tr>
        <td> Your Thumbnail photo is: </td><td><img src="<%= person.getThumbnailUrl() %>"/></td>
      </tr>
    </table>
    <h3>Your Friends are:</h3>
    <table border="1">
    <%
      for (OpenSocialPerson friend : friends) {
    %>
      <tr>
        <td>Id: <%= friend.getId() %></td>
        <td>
          <center>
            <img src="<%= friend.getThumbnailUrl() %>"/> <br/>
            <%= friend.getDisplayName() %> 
          </center>
        </td>
      </tr>
    <%
     }
    %>
    </table>
  </body>
</html>
```


<h4><a></a>How to customize the JSP app to fetch your own data</h4>
<p>The next logical step for this tutorial is to show you how to access your own list of friends from orkut, or other OpenSocial containers that support the RESTful protocol. Let's start by reviewing the steps needed which will allow you to access orkut with your own viewerId and your own friends. </p>
<p>In order to list your own friends, you'll recall that you need an OAuth consumer key and consumer secret, in addition to your own viewerId from orkut. </p>
<p>To retrieve an OAuth consumer key and secret, you will have had to install an application on orkut, and once successfully installed, you can then retrieve the consumer key and secret. Incidentally each OpenSocial container has their own way of providing access to a consumer key and secret, but for the sake of this tutorial, we'll quickly review how to go about retrieving them for use with orkut.</p>

<p><strong>Step 1. Install your own gadget application on orkut.  </strong></p>
<p>One of the key concepts behind 2-legged OAuth is that you are establishing that you are allowing a trusted set of users access content associated with a specific application. To that end, you will have to install an application on orkut and have it verified that it is yours. Once verified, you will then be able to generate an OAuth consumer key and secret, which can then be added to your server-side application (such as a JSP webapp) so that it can then access social data. </p>

<p>For this step you will need access to the OpenSocial sandbox environment at orkut. If you have not registered for an account, you'll need to go to <a href='http://sandbox.orkut.com'>http://sandbox.orkut.com</a> and register for a sandbox developer's account. Once registered you will be able to install your own OpenSocial gadgets onto your own profile at orkut. Typically you will need to build a gadget and host it somewhere on the Internet so you can then enter the url of the gadget into the orkut. For further information on building your first gadgets and installing them on orkut, please see the orkut developer's page at: <a href='http://code.google.com/apis/orkut/'><a href='http://code.google.com/apis/orkut/'>http://code.google.com/apis/orkut/</a></a></p>
<p>As an aid,  you can use the following gadget and install it on your own orkut sandbox account. </p>
<p><a href='http://chrisschalk.com/gadgets/sa/v0.8/basic/listfriends-tutorial.xml'><a href='http://chrisschalk.com/gadgets/sa/v0.8/basic/listfriends-tutorial.xml'>http://chrisschalk.com/gadgets/sa/v0.8/basic/listfriends-tutorial.xml</a></a></p>
<p>It is somewhat analagous to the JSP example in that it fetches and displays both viewer profile information as well as friends information. It is also helpful in that it also displays the viewer's orkut id on the page which is needed later when editing your OpenSocial client properties. </p>
<p><strong>Step 2. Verify your gadget to get an OAuth consumer key and secret. </strong></p>
<p>Once you've installed a gadget application onto orkut, you can use the Google gadget verifier tool to obtain an OAuth Consumer key and consumer secret. The verifier tool is available at: </p>
<p><a href='https://www.google.com/gadgets/directory/verify'><a href='https://www.google.com/gadgets/directory/verify'>https://www.google.com/gadgets/directory/verify</a> </a></p>
<p>Incidentally this tool will verify gadgets running on multiple Google related sites such as GMail and iGoogle in addition to orkut.</p>

<p><strong>Step 3: Edit your server-side code to add the new OAuth parameters and your own viewerId. </strong></p>
<p>After changing the consumer key and secret to your new values, you'll need to change the viewerId property to the id associated with your own user id on orkut. If you've used the example gadget mentioned above, you can simply look at the viewerId rendered in the gadget. Otherwise you can build your own gadget to obtain the viewerId using the OpenSocial JavaScript call <a href='http://wiki.opensocial.org/index.php?title=Opensocial.Person_%28v0.8%29#opensocial.Person.getId'>getId( )</a>. </p>
<p>Once you make these changes, you should be able to refresh the JSP page and see both your own profile data associated with your account, as well as a listing of your friends. </p>

## Summary ##
<p>As you have learned with this tutorial, it is possible to use the OpenSocial Java client libraries to enable your server-side Java Web apps to communicate with OpenSocial containers. You've also seen how to use the necessary OAuth parameters (consumer secret, and consumer key) in order to satisfy orkut's security requirements. This was shown by simply reusing a portion of the sample code provided with the OpenSocial Java client libraries and applying them to a JSP web app.</p>
<p>In addition to simply using existing OAuth parameters, and a viewerId associated with an existing  application along with a sample userId(viewId), we stepped through how you could go about generating your own OAuth parameters as well as getting your own viewerId by installing and verifiing your own OpenSocial application.</p>