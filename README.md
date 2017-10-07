# my-gdx-extension

Sometimes you just need some platform specific code which can be easily achieved by following the instructions from the<a href="https://github.com/libgdx/libgdx/wiki/Interfacing-with-platform-specific-code"> libGDX Wiki page</a>. However, in few cases (reusable code) you might consider writing your own libGDX extension for your own usage or maybe to share with the community. I have already written like 8 <a href="http://tomgrill.de/libgdx-extensions/">extensions for libGDX</a>. Few of them are released to the public.<!--more-->

Ok, let's get into it. At first, we need two projects:
<h2><strong>Project #1 (TEST-APP):</strong></h2>
A simple project created with the <a href="https://libgdx.badlogicgames.com/download.html">gdx-setup tool</a>. This project is just to test and play around with our extension. You can certainly use one of your existing game projects for this, but I rather prefer to keep things clean and separated.

These are my project setup values:

<em>Name: my-gdx-extension-app</em>
<em> Package: de.tomgrill.mygdxextensionapp</em>
<em> Game class: MyGdxExtensionApp</em>
<em> Sub-projects: all</em>
<em> Extensions: none</em>

Of course, you can find this <a href="https://github.com/TomGrill/my-gdx-extension-app">project on github.</a> Clone and checkout commit "15954a" and create a new branch if you want to follow along with this article.
<pre class="EnlighterJSRAW" data-enlighter-language="no-highlight">git clone git@github.com:TomGrill/my-gdx-extension-app.git
git checkout 15954a
git checkout -b my-new-branch</pre>
At this point (commit 15954a) there is nothing different to a fresh setup libgdx project.
<h2><strong>Project #2 (EXTENSION):</strong></h2>
There is also an existing project which you can clone to follow along:
<pre class="EnlighterJSRAW" data-enlighter-language="null">git clone git@github.com:TomGrill/my-gdx-extension.git
git checkout d6e89db
git checkout -b my-new-extension-branch</pre>
The basic directory structure looks like this.
<pre class="EnlighterJSRAW" data-enlighter-language="null">android/
   AndroidManifest.xml
   build.gradle
core/
   build.gradle
desktop/
   build.gradle
gradle/
   ...
html/
   build.gradle
ios/
   build.gradle
ios-moe/
   build.gradle
.gitignore
androidpublish.gradle
build.gradle
gradle.properties
gradlew
gradlew.bat
publish.gradle
settings.gradle</pre>
As you can see the directory structure looks very similar to the libgdx project above. However, there are a few important things which I will explain further.
<h2><strong>Rename packages and names</strong></h2>
I will list all files and the lines you might consider changing to an appropriate extension name.

<em>android/AndroidManifest.xml</em>
<pre class="EnlighterJSRAW" data-enlighter-language="null">package="de.tomgrill.mygdxextension.android"&gt;</pre>
<em>android/build.gradle</em>
<pre class="EnlighterJSRAW" data-enlighter-language="null">project.group = 'de.tomgrill.mygdxextension.android'
...
ext {
  ARTIFACTID = 'gdx-mygdxextension-android'
}</pre>
<em>core/build.gradle</em>
<pre class="EnlighterJSRAW" data-enlighter-language="null">ext {
  ARTIFACTID = 'gdx-mygdxextension-core'
}</pre>
<em>desktop/build.gradle</em>
<pre class="EnlighterJSRAW" data-enlighter-language="null">ext {
  ARTIFACTID = 'gdx-mygdxextension-desktop'
}</pre>
<em>html/build.gradle</em>
<pre class="EnlighterJSRAW" data-enlighter-language="null">ext {
  ARTIFACTID = 'gdx-mygdxextension-html'
}</pre>
<em>ios/build.gradle</em>
<pre class="EnlighterJSRAW" data-enlighter-language="null">ext {
  ARTIFACTID = 'gdx-mygdxextension-ios'
}</pre>
<em>ios-moe/build.gradle</em>
<pre class="EnlighterJSRAW" data-enlighter-language="null">ext {
  ARTIFACTID = 'gdx-mygdxextension-ios-moe'
}</pre>
<em>build.gradle</em>
<pre class="EnlighterJSRAW" data-enlighter-language="null">ext {
    GROUPID = 'de.tomgrill.mygdxextension'
    VERSION = '0.0.1-SNAPSHOT'
}

allprojects {
 ....

 ext {
    appName = 'gdx-mygdxextension'
    ...
 }

 ....
}</pre>
<em>publish.gradle
androidpublish.gradle</em>
<pre class="EnlighterJSRAW" data-enlighter-language="null">pom.project {
    name 'gdx-mygdxextension'
    description 'gdx-mygdxextension description'
    url 'https://github.com/TomGrill/my-gdx-extension'

    scm {
        url 'scm:git@github.com:TomGrill/my-gdx-extension.git'
        connection 'scm:git@github.com:TomGrill/my-gdx-extension.git'
        developerConnection 'scm:git@github.com:TomGrill/my-gdx-extension.git'
    }

    licenses {
        license {
            name 'The Apache Software License, Version 2.0'
            url 'http://www.apache.org/licenses/LICENSE-2.0.txt'
            distribution 'repo'
        }
    }

    developers {
        developer {
            id 'tomgrill'
            name 'Thomas Pronold'
            email 'mail@tomgrill.de'
        }
    }
}</pre>
Edit according to your needs.
<h3><strong>Why has Android a separate androidpublish.gradle file?</strong></h3>
<strong>Reason 1</strong>: In case you develop using Java 8, the JDK is very strict when it comes to JavaDoc generation. So you could either disable JavaDoc generation (not good if you want to share your extension some day) or tell the JDK that it should behave like a Java 7 JDK and compile even when the JavaDoc is not super perfect.

<strong>Reason 2</strong>: We want the Android part of the extension to be a library instead of an Android project.

<em>Hint: Keep your naming consistent to avoid stupid bug hunting.</em>

&nbsp;
<h2>Publish your extension:</h2>
For the local use you can now publish your extension with this simple line of command line code:
<pre class="EnlighterJSRAW" data-enlighter-language="null">./gradlew clean uploadArchives -PLOCAL=true</pre>
Now you can use your extension within your game projects, just add the dependencies to your game projects like this:

<em>test-app/build.gradle</em>
<pre class="EnlighterJSRAW" data-enlighter-highlight="8">project(":desktop") {   
    ...
    dependencies {
        compile project(":core")
        compile "com.badlogicgames.gdx:gdx-backend-lwjgl:$gdxVersion"
        compile "com.badlogicgames.gdx:gdx-platform:$gdxVersion:natives-desktop"

        compile "de.tomgrill.mygdxextension:gdx-mygdxextension-desktop:0.0.1-SNAPSHOT"
    }
}</pre>
For all the other sub-projects just add the corresponding lines:
<pre class="EnlighterJSRAW" data-enlighter-language="null">compile "de.tomgrill.mygdxextension:gdx-mygdxextension-android:0.0.1-SNAPSHOT"
compile "de.tomgrill.mygdxextension:gdx-mygdxextension-html:0.0.1-SNAPSHOT"
compile "de.tomgrill.mygdxextension:gdx-mygdxextension-ios:0.0.1-SNAPSHOT"
compile "de.tomgrill.mygdxextension:gdx-mygdxextension-ios-moe:0.0.1-SNAPSHOT"
compile "de.tomgrill.mygdxextension:gdx-mygdxextension-core:0.0.1-SNAPSHOT"</pre>
Refresh your gradle dependencies each time you publish a new version of the extension.

That's basically all. Within the next part of this article series, we will discover loading and adding platform specific code to our extension.
<h2>A few words if you plan to publish your extension to the public.</h2>
In order to do that you have to publish your extension to a repository which can be accessed by others. The libGDX guys chose <a href="https://oss.sonatype.org">sonatype</a> which I recommend to do as well because this repository is already added to the repositories list in the <em>build.gradle</em> file:
<pre class="EnlighterJSRAW" data-enlighter-language="null">maven { url "https://oss.sonatype.org/content/repositories/snapshots/" }
maven { url "https://oss.sonatype.org/content/repositories/releases/" }</pre>
And the <em>publish.gradle</em> default values are already setup to use this repository.

You can create an account following the steps on this website: <a href="http://central.sonatype.org/pages/ossrh-guide.html">http://central.sonatype.org/pages/ossrh-guide.html</a>

After you have an account upload your extensions with the following commands.

Snapshot release:
<pre class="EnlighterJSRAW" data-enlighter-language="null">./gradlew clean  uploadArchives -PSNAPSHOT=true -PNEXUS_USERNAME=YOURUSERNAME -PNEXUS_PASSWORD=YOURPASSWORD</pre>
Release release :)
<pre class="EnlighterJSRAW" data-enlighter-language="null">./gradlew clean uploadArchives -PRELEASE=true -PNEXUS_USERNAME=YOUR-USERNAME -PNEXUS_PASSWORD=YOU-PASSWORD -Psigning.keyId=12345ABC -Psigning.password=YOUR-SIGNING-PASSWORD -Psigning.secretKeyRingFile=/path/to/certificate/secring.gpg</pre>
Look at the <em>publish.gradle</em> file to understand how parameters are passed to get a better understanding.

&nbsp;

&nbsp;

## Updates & News
Follow me to receive release updates about this and my other projects (Promise: No BS posts)

https://twitter.com/TomGrillGames and https://www.facebook.com/tomgrillgames

I will also stream sometimes when developing at https://www.twitch.tv/tomgrill and write a blog article from time to time at http://tomgrill.de 

# License

The project is licensed under the Apache 2 License, meaning you can use it free of charge, without strings attached in commercial and non-commercial projects. We love to get (non-mandatory) credit in case you release a game or app using this project!
