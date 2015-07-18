# JAudioLibs #

The various JAudioLibs should be backwards compatible with each other, but for best results please use matching versions.

## JAudioLibs build 120912 ##

### Changes ###

  * JNAJack
    * Added support for client and port registration callbacks, port connection callbacks, graph reorder callbacks and querying port connections. A huge thank you to Chuck Ritola for providing the bulk of this code. Fixes [issue #4](https://code.google.com/p/java-audio-utils/issues/detail?id=#4).  See [JNAJack - listening for graph changes](JNAJack_GraphChanges.md) for example usage.
  * AudioServers
    * The time passed into the client from the JavasoundAudioServer now compensates for jitter between the system clock and soundcard clock. This uses a port of code from http://code.google.com/p/libtimefilter/


### Bug Fixes ###

  * JNAJack
    * Fixed linking with JACK on Windows with a 64bit JVM. This is due to the library name being libjack64 instead of libjack.
  * AudioServers
    * Fixed a bug in the JavasoundAudioServer that would cause it to choose an invalid mixer in certain circumstances.



---


## JAudioLibs v1.0.120123 ##

### Changes ###

  * JNAJack
    * Big performance improvement through use of JNA's new CallbackThreadInitializer mechanism (requested by this project) when using JNA 3.4.0+.  JNAJack will still work with earlier versions of JNA, but this is not recommended and will emit a warning.  To suppress the warning if you absolutely have to use an earlier version of JNA, or to switch to the old behaviour, set the system property `jnajack.disable-cti` to `true` before your first call to `Jack.getInstance()`
  * AudioServers
    * No changes from v1.0.111130


---


## JAudioLibs v1.0.111130 ##

NB. JNAJack now requires JACK1 version 0.118.3+ or JACK2 version 1.9.3+

### Changes ###

  * JNAJack
    * Performance improvement by switching to JNA's direct mapping mode, and better buffer management.
    * Port connection API changed to be more robust, and more accurately map JACK's API.  [Jack.connect(String source, String destination)](http://java-audio-utils.googlecode.com/hg/javadoc/jnajack/org/jaudiolibs/jnajack/Jack.html#connect%28java.lang.String,%20java.lang.String%29) is deprecated and replaced with [Jack.connect(JackClient client, String source, String destination)](http://java-audio-utils.googlecode.com/hg/javadoc/jnajack/org/jaudiolibs/jnajack/Jack.html#connect%28org.jaudiolibs.jnajack.JackClient,%20java.lang.String,%20java.lang.String%29).  Likewise the disconnect methods.
  * AudioServers
    * [JackAudioServer](http://java-audio-utils.googlecode.com/hg/javadoc/audioservers/org/jaudiolibs/audioservers/jack/JackAudioServer.html) is updated to make use of the revised JNAJack connection API above.  Auto-connect now works more consistently!


### Bug Fixes ###

  * JNAJack
    * getPorts() no longer throws an UnsatisfiedLinkError on Windows - fixes [issue 1](https://code.google.com/p/java-audio-utils/issues/detail?id=1) (and possibly the cause of [issue 2](https://code.google.com/p/java-audio-utils/issues/detail?id=2)).