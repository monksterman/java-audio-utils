# Getting Started with the AudioServers API #

  1. First, download the AudioServers archive from the Downloads page.  If you want to use the JACK audio server you'll also need the JNAJack and JNA downloads.
  1. To make use of the AudioServers API, you just need to implement the [AudioClient](http://java-audio-utils.googlecode.com/hg/javadoc/audioservers/org/jaudiolibs/audioservers/AudioClient.html) interface in your code.  To make use of the JavaSound server you need to include the audioservers and audioservers-javasound jar files in your classpath.  To also use JACK, you'll need the audioservers-jack, JNAJack and JNA jar files in your classpath.
  1. Create an audio server using the `create()` method in either the [JavasoundAudioServer](http://java-audio-utils.googlecode.com/hg/javadoc/audioservers/org/jaudiolibs/audioservers/javasound/JavasoundAudioServer.html) or the [JackAudioServer](http://java-audio-utils.googlecode.com/hg/javadoc/audioservers/org/jaudiolibs/audioservers/jack/JackAudioServer.html), and pass in your client.
  1. The `run()` method of the server will be run in the calling thread, so you may wish to create a (high-priority) thread to run this in.
  1. The client `configure()` method will be called. **DO NOT** assume that the [AudioConfiguration](http://java-audio-utils.googlecode.com/hg/javadoc/audioservers/org/jaudiolibs/audioservers/AudioConfiguration.html) you passed into the server create() method is the same.  The AudioServer implementation will give you its closest match.  Throw an Exception if your code cannot handle the provided configuration.
  1. The client `process()` method will then be called for each audio buffer. You can return `false` from this method to stop the server processing audio.
  1. The client `shutdown()` method will be called when the AudioServer is shut down so that the client can clean up after itself.

Have a look at the [full JavaDoc](http://java-audio-utils.googlecode.com/hg/javadoc/audioservers/index.html)

## Performance ##

You are recommended to run Java with the -Xincgc option, or other options which reduce pauses caused by the garbage collector.

To get low-latency with JavaSound, make sure to pass in [TimingMode.Estimated](http://java-audio-utils.googlecode.com/hg/javadoc/audioservers/org/jaudiolibs/audioservers/javasound/JavasoundAudioServer.TimingMode.html#Estimated) or [TimingMode.FramePosition](http://java-audio-utils.googlecode.com/hg/javadoc/audioservers/org/jaudiolibs/audioservers/javasound/JavasoundAudioServer.TimingMode.html#FramePosition) into the `create()` method of the server. Praxis uses Estimated by default.

**DO NOT** use synchronized methods or otherwise cause the audio callback to block. To pass information into the audio processing thread, consider using a non-blocking concurrent queue. A great article on all this - http://www.rossbencina.com/code/real-time-audio-programming-101-time-waits-for-nothing

You can get away with (minimal) object allocation in the callback thread, due to the way Java pools memory, however don't overdo it - and remember, lots of objects cause lots of garbage which will need to be collected.