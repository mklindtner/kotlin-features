# WebSockets Android


## Introduction
Using websockets on android is quite unusual, instead we usually want to ask a http connection for a json output and go from there. When that is noted, there may be exceptions and we really need to have a constant connection to something/someone. Just remember to <i> really </i> ask yourself if you need it.


### Setup
#### acitivity_main.xml
simply setup a button and a textview. Self explanatory
``` kotlin
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".MainActivity">

    <Button
            android:id="@+id/start"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Start !"
            android:layout_margin="40dp"
            android:textSize="17sp"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintStart_toStartOf="@+id/output"

    />


    <TextView
            android:id="@+id/output"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_below="@id/start"
            android:layout_centerHorizontal="true"
            android:textSize="16sp"
            android:layout_marginTop="30dp"
            app:layout_constraintTop_toTopOf="@+id/start"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toStartOf="parent"
    />

</android.support.constraint.ConstraintLayout>

```
####  AndroidManifest.xml
just allow for internet permission
``` kotlin
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="com.mkl.websocket.android">

    <uses-permission android:name="android.permission.INTERNET" />

    <application
            android:allowBackup="true"
            android:icon="@mipmap/ic_launcher"
            android:label="@string/app_name"
            android:roundIcon="@mipmap/ic_launcher_round"
            android:supportsRtl="true"
            android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>

                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
    </application>

</manifest>
```


#### gradle
add okhttp3 under dependencies
``` kotlin
apply plugin: 'com.android.application'

apply plugin: 'kotlin-android'

apply plugin: 'kotlin-android-extensions'

android {
    compileSdkVersion 28
    defaultConfig {
        applicationId "com.mkl.websocket.android"
        minSdkVersion 15
        targetSdkVersion 28
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation"org.jetbrains.kotlin:kotlin-stdlib-jdk7:$kotlin_version"
    implementation 'com.android.support:appcompat-v7:28.0.0'
    implementation 'com.android.support.constraint:constraint-layout:1.1.3'
    implementation 'com.squareup.okhttp3:okhttp:3.6.0'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'com.android.support.test:runner:1.0.2'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'
}
```


### Create websocket
in this example we'll just use our MainActivity to write the websocket on. For starters we wish to do:
1. set an eventListener to our socket (in this example a button)
2. setup/find a point for our websocket to go through (i.e. a website) where we can exchange information
3. create an overview for how to implement the websocket
4. implement the actual websocket
5. use OkHttpClient (or whichever lib you prefer)  to listen
6. shutdown the service once the purpose of the socket is done. 


1. to do this we simply setup an onClickEvent listener on our button "start" and creates a method for what start should do,
aptly named .... "start()".....
``` kotlin
   override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        start.setOnClickListener { start() }
    }

```
2. 
google-fu shows that a website "ws://echo.websocket.org" listens to websockets and we can send our amazing socket there.

3.
We can send a request there to open up a connection and make a general outline for how we want our websocket to work.
``` kotlin
  private fun start() {
        var request = Request.Builder().url("ws://echo.websocket.org").build() //error here w. website
        var listener = EchoWebSocketListener()
        var client = OkHttpClient() //remember to import me 
        client.newWebSocket(request, listener)

        client.dispatcher().executorService().shutdown()
    }
```
what we do:
we make the request to the website, then we create our websocket. Afterwards we imitate a client using "okHttpClient".
We then we add the websocket to the client (in case it's not clear, a websocket takes a request and requesteé. Better known as a listenener), and lastely we shutdown the service immediately afterwards becuase this is an example and we can do everything we want to on the "onOpen" method we're about to implement. Normally we would require a condition before shutting down.

4.
google-fu will show us that "WebSocketListener()" is a library within android that implements... a websocketListener ... 
so we do that: 
``` kotlin
class EchoWebSocketListener : WebSocketListener() {
        val NORMAL_CLOSURE_STATUS = 1000

        override fun onOpen(webSocket: WebSocket?, response: Response?) {
            webSocket?.send("Hello there I am a figurative friend")
            webSocket?.send("oh no I need to go")
            webSocket?.send(ByteString.decodeHex("I AM DECODING on hex, ayyy"))
            webSocket?.close(NORMAL_CLOSURE_STATUS, "goodbye Mikkel, stop being so mean !")
        }

        override fun onMessage(webSocket: WebSocket?, text: String?) {
            outputTextbox("Receiving:$text")
        }


        override fun onMessage(webSocket: WebSocket?, bytes: ByteString?) {
            outputTextbox("Receiving:" + bytes?.hex())

        }

        override fun onClosing(webSocket: WebSocket?, code: Int, reason: String?) {
            webSocket?.close(NORMAL_CLOSURE_STATUS, null)
            outputTextbox("Closing: $code/$reason")
        }

        override fun onFailure(webSocket: WebSocket?, t: Throwable?, response: Response?) {
            outputTextbox("Error:" + t?.message)
        }
    }
```
in here we simply override some methods to show the most common examples. "onMessage" recieves a string or a list of bytes represented as a string and turns that into a message. We use the onOpen to send our message. Normally we would do some setup/maintence here and the requester would be the one to send us some info. Since our requester is a website we just skip that ... Lastly we need to define how we wish to handle our outputTextBox() method. 
``` kotlin 
 private fun outputTextbox(txt: String) {
        this@MainActivity.runOnUiThread {
            object : Runnable {
                override fun run() {
                    var res = output.getText().toString() + "\n\n" + txt
                    output.text = res
                    //outputTextbox.setText(outputTextbox.getText().toString() + "\n\n" + txt)
                }
            }
        }
    }
```
