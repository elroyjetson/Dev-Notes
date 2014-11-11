Cordova Android Development Tips
================================

### Introduction

Way back in the early days, well 2011 seems like forever ago in mobile
Internet time, I had built an app with [PhoneGap](http://phonegap.com).
It wasn’t a pleasant experience and involved a lot of digging around
native code to make things work as well as the use of Eclipse, which is
simply cruel and unusual punishment. Fast forward to 2014, I was brought
into a discussion about building an Android app, based on the iOS app
that had already been created, in a short development time frame. It
involved the use of the camera to scan bar codes so a simple web app had
been ruled out. After my 2011 experience, I was hesitant to go down the
path of building a PhoneGap app again, so we discussed what it would
take to build a native Android app. Internally, none of the developers
were familiar with the Android SDK to the point that turning around an
app in a few short weeks was going to work. We decided to try bringing
in an Android developer. We felt that the project could be down in two
weeks if they knew the Android SDK well enough. The search for an
Android developer was much more difficult than one would expect. After
contacting a few people that I know, they were all either booked, or no
longer did development work themselves. We thought about several other
possibilities to outsource this effort, but felt that the shear amount
of time to get someone fully up to speed, or the shady reputations of
some of the unknown developers that were interested, was just not going
to work. So I decided to give PhoneGap another try.

### Why Cordova and not PhoneGap {#whycordovaandnotphonegap}

As I explored PhoneGap, I found that it had changed so much since 2011
that I needed to basically approach it as if I had never used it before.
The company behind PhoneGap had since been acquired by Adobe and I now
had the open source Cordova project or the Adobe branded PhoneGap
project to decide on. I started reading the online documentation,
attempting to determine what the differences were between the two
projects and in a cursory review could find little difference. My gut
said that PhoneGap would be more polished since it had paid staff
devoted to it whereas Cordova was a community project. But as I started
to dig into this, it is not what I found. This could simply have been a
product of the few web searches I did worked out better for me with
Cordova than PhoneGap, but after about 30 minutes, I felt much more
comfortable with Cordova than I did PhoneGap. Your experience may be
different. I would love to have an official comparison of why one would
choose PhoneGap over Cordova.

### 15 minutes and the first bar code was scanned {#minutesandthefirstbarcodewasscanned}

To understand the scope of the project, it is an application designed
for scanning QR Codes on badges at trade shows to build up leads. It has
the ability to quickly qualify leads with various options as well as add
a short note. It lists the leads and you can edit a lead that was
previously scanned. The app activates to a server and leads are synced
to that server and can then be managed in a web interface. For version
one, while it syncs to the server, it does not update back to the app.
So changes are not reflected that are made online. The big component is
the badge scanning. This is the item that I feared the most since
accessing hardware and plugins in my past PhoneGap experience was
neither easy, nor reliable. I chose to use the stock PhoneGap
BarcodeScanner plugin for simplicity as shown in the Cordova
documentation
^[1](http://cordova.apache.org/docs/en/3.3.0/guide_cli_index.md.html#The%20Command-Line%20Interface_add_plugin_features)^,
and hoped it would be good enough. In the end, it turned out that it
didn’t work, so I did some searching and came up with a fork that worked
well ^[2](https://github.com/jonathannaguin/BarcodeScanner)^. Cordova
changed things at some point in the past that everything works as a
plugin now. That is external plugins as well as cordova api items. This
actually makes things really nice. So here is the 15 minute build
instructions based on the HelloWorld project
^[2](https://github.com/jonathannaguin/BarcodeScanner)^.

    $sudo npm install -g cordova
    $cordova create hello org.elroyjetson.hello HelloWorld
    $cd hello
    $cordova platform add android
    $cordova plugin add https://github.com/jonathannaguin/BarcodeScanner.git

At this point I simply grabbed the chunk of the sample code from the
BarcodeScanner git repo to use to test. Opened up www/js/index.js and
dropped it in at the end of the receivedEvent function that is in as
part of the HelloWorld app.

    $cordova build

The build completed with now errors, so next thing I did was plugged in
my Moto X with USB debugging turned on to run the app. You may need to
make sure that adb is listening for devices, that is easy enough:

    $adb device

If you see some output indicating a device, you are good to go, so time
to run the app.

    $cordova run

This takes a lot longer than you might expected, but the app installs,
opens up and immediately launches the scanner, scan a bar code and got
the results. This probably would have taken less time had I been able to
simply install the barcode scanner like in the docs, but this worked
great. From this point, all you need to do is build the app like you
would for any mobile web project. In the next section, I will go over
some tips and tricks that I learned along the way that might help your
experience go just a little smoother.

### Development Tips {#developmenttips}

#### [Tip: 1 - Project setup](#tip1) {#tip:1-projectsetup}

As I began to write my code I built everything off of the app object
that already existed, adding on module objects as I went, handling
routing and data through custom objects that I created as I needed them.
This was a big mistake from the outset, and I kick myself because I
should have known better. If you are going to build a single page app,
which is the recommend way in a Cordova project, do yourself a favor and
use [BackBone.js](http://backbonejs.org). Doing this would have shaved
several days off of my development time along with making the code
easier to test and maintain.

#### [Tip: 2 - Build all web functionality and screens in the browser first](#tip2) {#tip:2-buildallwebfunctionalityandscreensinthebrowserfirst}

While development tools have come a long way, and you will be using
<chrome://inspect> heavily, it would have been much faster to follow a
normal RWD, mobile first, work flow for developing all of the app
screens and basic, e.g. non-device functionality, before heading to the
device.

#### [Tip: 3 - Create your own console.log method](#tip3) {#tip:3-createyourownconsole.logmethod}

You can use console.log to push out debug messages in your app, but then
you will need to remove them all for a final release. This become a
problem later if you want to use them for follow on app versions. The
easiest way to deal with this was to add an attribute to your app, in my
case app.debug, and set it to true or false. Then create a method that
checks this, if it is true, show the console.log method, if it is false
don’t. Here is the log method I used and this becomes handy for [Tip:
4](#tip4)

    log: function(msg) {
        if(app.debug) {
            var date = new Date();
            console.log(date + ': ' + msg);
        }
    }

There may be better ways of handling this, but this worked well for me.
The other thing I did with this is any time I logged a message I always
add “Method: method.name” to the front of it so I knew exactly what was
spitting out the message.

#### [Tip: 4 - Debugging older Android devices](#tip4) {#tip:4-debuggingolderandroiddevices}

Since I had a test device for Android 2.3.3 API version 10, and
according to the latest Android stats at the time
^[4](http://developer.android.com/about/dashboards/index.html)^, API
version 10 on up covers 98.7% of installed Android devices, I set out to
work with my min API version set to 10. Older Android webview do not,
unfortunately, use Chrome so using Chrome://inspect will not be able to
help you for debugging. While that makes things harder, it isn’t
impossible. If you followed [Tip 2](#tip2) and built all of your
interface using a RWD, mobile first, work flow, most of your time will
be spent debugging JavaScript. I did a lot of this with console.log (or
a modified version see [Tip: 3](#tip3)). This does take a little
knowledge of [adb](http://developer.android.com/tools/help/adb.html).
But the Cordova environment spits out all of these messages and more in
the Android debug log and filtering them is pretty easy, and trust me,
you want to filter. In my project I also used the [Web SQL
API](http://www.w3.org/TR/webdatabase/) since Indexdb is not supported
well yet and I needed to perform some more complicated queries than is
feasible with the Localstorage API. To do this we just filter on what we
need:

    $adb logcat -s "CordovaLog","Database"

#### [Tip: 5 - Creating Android icons and splash screens](#tip5) {#tip:5-creatingandroidiconsandsplashscreens}

Creating Android icons and splash screens proved particularly tricky.
Like the web, Android has an incredible amount of device diversity. So
this needs to be taken into account during the design phase of your
project. Keeping in mind that you need an adequate amount of padding
around any bit mapped portions of your images that you do not want to be
stretched to fit. Google has provided a good deal of resource on the
subject that you should read as part of the design process
^[5](http://developer.android.com/guide/practices/screens_support.html)^.
Android uses a special file format call the Nine Patch image that helps
to prevent your icons and splash screens from becoming distorted. The
Android SDK ships with a tool for creating these images, but another
handy tool to generate them is a tool called the [Simple Nine-Patch
Generator](http://android-ui-utils.googlecode.com/hg/asset-studio/dist/nine-patches.html)
and I found it works really well. The Cordova documentation does discuss
putting the icons in your www/res/platform folder but I found two
problem with this. First, now the res folder is compiled into the final
apk file that is loaded on the device, so it will be loaded twice, and,
second, it Cordova doesn’t actually do anything with these resources —
at least that I could figure out. Since you have to manually place them
in the correct platform res/drawable-\* directories, it’s best to move
this folder outside of the www source to save the file size. There are
two things you need to do, aside from move the splash screen assets to
the correct res/drawable-\* directories, if you are using a splash
screen. These changes are made to the config.xml file for your project
in the www source folder.

    <preference name="SplashScreen" value="ic_launcher" />
    <preference name="SplashScreenDelay" value="3000" />

The “SplashScreen” option tells Android the resource root name to use
for your splash screen and “SplashScreenDelay” indicates how long to
display the splash screen in milliseconds.

#### [Tip: 6 - Creating Android release APK](#tip6) {#tip:6-creatingandroidreleaseapk}

This took the most digging and, in the end, I couldn’t get Cordova to
produce a signed release version of the app, I set it up to work using
Apache Ant, which is what Cordova is using underneath. I am sure there
is a way to get this right using Cordova build, but the flow is not well
documented and it was just easier to by pass it all together. Google has
a nice document on generating a self signed key and keystore for your
application
^[6](http://developer.android.com/tools/publishing/app-signing.html#cert)^.
Once you have generated the keystore, make note of where you placed it
and **BACK IT UP**. You cannot release updates to the app once it has
been submitted without this key so don’t lose it. Once you have the
keystore and keys created you can skip the rest of the Google doc,
because Apache Ant will handle that for you. We need to create several
files to configure Ant to handle the release build when we run it.
First, create the file called custom\_rules.xml in the root (the same
directory as the AndroidManifest.xml) directory of your Android
platform, and place these lines of XML in it:

    <?xml version="1.0" encoding="UTF-8"?>
    <project name="custom_rules" default="help">
      <property file="secure.properties" />
    </project>

Apache Ant is setup by default to look for that file in it’s build.xml
configuration file. Now we need to create the secure.properties file we
just told it about. This file is going to contain the location on your
system of your keystore file as well as the keystore password and the
keystore alias password, so you probably should exclude that from source
code control so as not to check in your passwords. In this file will be
four lines using the information created earlier.

    key.store=/path/to/kestore/file/keystorefile.name
    key.store.password=YourKeystoreFilePassword
    key.alias=YourKeyAliasName
    key.alias.password=YourAliasPassword

No to generate a release build, that will sign and align your APK,
change into the Android platform directory and then run the ant release
command:

    $ant release

You can now find the release version of the APK in the
platforms/android/bin directory. You will see other version,
appname-debug.apk, appname-release-unsigned.apk etc. The one you submit
to the Play Store is appname-release.apk.

#### [Tip: 7 - Updating build release numbers](#tip7) {#tip:7-updatingbuildreleasenumbers}

To increment your build release numbers, a requirement for each release
in the Play Store, you edit your project config.xml file. The first line
should be a widget line, just increment the version attribute and then
rebuild to get the new version number in your APK.

### Conclusion

Cordova/PhoneGap has come a long way and is constantly getting better.
There is a lot of customization that you can do that I didn’t even get a
chance to dig into, but hope to in the future. When I get some time, I
will put together some of the more Android development specific tips
that I have accumulated as part of this project so it, hopefully, won’t
take you as long to find it all for your projects.

