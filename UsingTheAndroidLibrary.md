# Introduction #
In the org/opensocial/android directory you will find additional source that makes writing opensocial enabled android apps blissfully easy.


# The sample android app #

Inside the java/android directory is a fully working android app that allows the user to choose their favorite OpenSocial provider and view their friends.

To run this on your own android emulator:
  * Install the android sdk: http://code.google.com/android/intro/index.html
  * Modify the build.xml file to point to your android sdk location
    * In java/android/build.xml set the "sdk-folder" and "android-tools" properties
    * An example would look like:
```
   <!-- SDK Locations -->
  <property name="sdk-folder" value="/home/doll/android-sdk-linux_x86-1.0_r1" />
  <property name="android-tools" value="/home/doll/android-sdk-linux_x86-1.0_r1/tools" />
```
  * running the install target in the build file will load up the project in the currently connected device (an emulator or a plugged in phone)

  * Note: You can also pull this code into an android eclipse project and run from there.


In the sample app, everything in the src/sample code directory should probably be modified. The src/org/`*` code on the other hand is a symlink to the library code. It takes care of all of the OAuth, browser, user interaction details and shouldn't need any changes.


# To enable an existing android app #

First, you need to depend on the android library code.
  * The java code in java/src/org needs to be included or symlinked into your project file. (The sample uses symlinks)
  * The jars in the android/libs directory should be copied into your own libs dir.

Next, modify your AndroidManifest.xml file to
  * Register the OpenSocial chooser: `<activity android:name="org.opensocial.OpenSocialChooserActivity"></activity>`
  * Register an additional intent:
```
      <intent-filter>
        <action android:name="android.intent.action.VIEW"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <category android:name="android.intent.category.BROWSABLE"/>
        <data android:scheme="REPLACE WITH YOUR APP SPECIFIC SCHEME"/> 
      </intent-filter>
```
  * Request internet permission `<uses-permission android:name="android.permission.INTERNET"></uses-permission>`

Last, take any existing activity that you want to get OpenSocial information and have it extend `OpenSocialActivity`. That activity can now get an OpenSocialClient and fetch friends or other information:

```
  private static String ANDROID_SCHEME = "REPLACE WITH YOUR APP SPECIFIC SCHEME";
  private static Map<OpenSocialProvider, Token> SUPPORTED_PROVIDERS
      = new HashMap<OpenSocialProvider, Token>();

  static {
    // Setup all the OpenSocial containers you want to integrate with
    // and set your specific consumer token, if the container requires one.
    // example: SUPPORTED_PROVIDERS.put(OpenSocialProvider.MYSPACE,
    //     new Token(<your consumer key like "http://www.myspace.com/..." >, <your secret>));
    SUPPORTED_PROVIDERS.put(OpenSocialProvider.PLAXO, new Token("anonymous", ""));
  }

  public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    OpenSocialClient client = getOpenSocialClient(SUPPORTED_PROVIDERS, ANDROID_SCHEME);

    // If the client is null the OpenSocialChooserActivity will be started
    if (client != null) {
      List<OpenSocialPerson> friends = client.fetchFriends();
    }
  }

```
