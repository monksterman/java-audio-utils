# Getting Started with JNAJack #

JNAJack provides a minimal Java object-oriented wrapper to the majority of the audio functions within JACK. If you are looking to write a basic audio client for JACK in Java you are recommended to look at the [AudioServers API](GettingStarted_AudioServers.md) as this will allow you to define an audio client using a simple interface that can be used by any AudioServer implementation (currently JACK and JavaSound).

## Testing JNAJack ##

To test out JNAJack, download JNAJack and JNA from the downloads page. Unzip JNAJack, and put the JNAJack JAR file in the same directory as JNA. Make sure JACK is running and try the following command line.

NB. If all runs OK, this will automatically connect to JACK, so will immediately start playing a sound!

```
java -Xincgc -cp jna.jar:org-jaudiolibs-jnajack.jar org.jaudiolibs.jnajack.examples.SineAudioSource
```

On Windows, you need to change the classpath separator to a semicolon

```
java -Xincgc -cp jna.jar;org-jaudiolibs-jnajack.jar org.jaudiolibs.jnajack.examples.SineAudioSource
```


## Differences to the standard JACK API ##

JNAJack has some key difference to the C API in that it is object-oriented, both to give some more type safety when calling out from Java, and to make the API more Java-like.

  * To get access to the main JACK object use Jack.getInstance() See [Jack JavaDoc](http://java-audio-utils.googlecode.com/hg/javadoc/jnajack/org/jaudiolibs/jnajack/Jack.html)
  * To create a client you use Jack.getInstance().openClient(...) - this returns a JackClient object.
  * Most functions that in C contain a pointer to a JACK client instance are methods on a JackClient object.  See [JackClient JavaDoc](http://java-audio-utils.googlecode.com/hg/javadoc/jnajack/org/jaudiolibs/jnajack/JackClient.html)
  * Ports are created using JackClient.registerPort(), which returns a JackPort object. Functions that in C take a pointer to a JACK port are methods on a JackPort object. See [JackPort JavaDoc](http://java-audio-utils.googlecode.com/hg/javadoc/jnajack/org/jaudiolibs/jnajack/JackPort.html)
  * Callbacks are standard Java interfaces and are registered on a JackClient instance.
  * C enums are converted to Java Enums, and functions that take a bitset in C now take an EnumSet in Java. This is done to ensure invalid flags cannot be sent out of the JVM.

For more information have a look at the [full JavaDoc](http://java-audio-utils.googlecode.com/hg/javadoc/jnajack/index.html)

For more pointers, take a look at the source for the [JackAudioServer](http://code.google.com/p/praxis/source/browse/audio.servers.jack/src/org/jaudiolibs/audioservers/jack/JackAudioServer.java) implementation from the AudioServers API.

## Performance ##

You are recommended to run Java with the -Xincgc option, or other options which reduce pauses caused by the garbage collector.

**DO NOT** use synchronized methods or otherwise cause the audio callback to block. To pass information into the audio processing thread, consider using a non-blocking concurrent queue. A great article on all this - http://www.rossbencina.com/code/real-time-audio-programming-101-time-waits-for-nothing

You can get away with (minimal) object allocation in the callback thread, due to the way Java pools memory, however don't overdo it - and remember, lots of objects cause lots of garbage which will need to be collected.