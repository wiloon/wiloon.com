---
title: jvm thread DestroyJavaVM
author: wiloon
type: post
date: 2017-03-30T07:30:51+00:00
url: /?p=9996
categories:
  - Uncategorized

---
  1. DestroyJavaVM is a thread that unloads the Java VM on program exit. Most of the time it should be waiting, until apocalypse of your VM.
  2. Signal Dispatcher is a thread that handles the native signals sent by the OS to your jvm.
  3. Finalizer threads pull objects from the finalization queue and calls it finalize method.
  4. Reference Handler is a high-priority thread to enqueue pending <a href="http://docs.oracle.com/javase/7/docs/api/java/lang/ref/Reference.html" rel="noreferrer">References</a>. Its defined in java.lang.ref.References.java

&nbsp;

This happens because most applications are run in threads.

All POJO apps start by invoking the `main` method. In it&#8217;s most simple case this method will do all of the work, creating objects, calling methods etc. Once `main` completes, the JVM is told to shut down using a `DestroyJavaVM` thread which waits for all threads to complete before doing it&#8217;s work. This is to ensure that any threads you create run to completion before the JVM is torn down.

An app with a GUI, however, normally runs as a number of threads. One for watching for system events such as keyboard or mouse events. One for maintaining the windows and display etc. The `main` method of this kind of app will probably just start up all the required threads and exit. It still creates the `DestroyJavaVM` thread but now all that does is wait for all of your created threads to finish before tearing down the VM.

As a result, any app that creates threads and relies solely on their functionality will allways have a `DestroyJavaVM` thread waiting for it to finish. Since all it is doing is `join`ing all other running threads it does not consume any resources.

&nbsp;

http://stackoverflow.com/questions/5766026/default-threads-like-destroyjavavm-reference-handler-signal-dispatcherhttp://stackoverflow.com/questions/34433267/destroyjavavm-thread-always-running