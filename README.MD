# Ping for Android in Kotlin

[![CircleCI](https://circleci.com/gh/igorskh/kt_ping.svg?style=svg)](https://circleci.com/gh/igorskh/kt_ping)
[![Codacy Badge](https://api.codacy.com/project/badge/Grade/66032728618f467990c4a6d60abdced0)](https://www.codacy.com/app/igorskh/kt_ping?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=igorskh/kt_ping&amp;utm_campaign=Badge_Grade)

## Description
This is a demonstration of an ICMP ping application for Android with configurable size and interval. The application executes a system ping process with defined paramteres and uses a simple regex to parse values. Some very basic exceptions handling is implemented.

The figure below shows a screenshot of a simulated run.

![](public/screenshot.png)

## Implementation details
The main application logic is located in [MainActivity.kt](app/src/main/java/ru/roundeasy/ktping/MainActivity.kt).

Class `class PingProcess : Runnable` contains a ping thread, it reads parameters from the inputs and forms a string array `cmd`

```kotlin
val serverAddr = textServer.text.toString()
val size = textSize.text.toString()
val interval = textInterval.text.toString()

val cmd = mutableListOf("ping", "-D")
if (size != "") {
    cmd.addAll(arrayOf("-s", size))
}
if (interval != "") {
    cmd.addAll(arrayOf("-i", interval))
}
cmd.add(serverAddr)
```

Then, we create a `process` and initialize buffer reader

```kotlin
val process = Runtime.getRuntime().exec(cmd.toTypedArray())
val stdInput = BufferedReader(InputStreamReader(process.inputStream))
```

Considering that ping runs indefinetely, we are continiously reading process output until we stop it and send it to the main thread to update the UI

```kotlin
for (l in stdInput.lines()) {
    if (!isThreadRunning) {
        break
    }
    lastPingString = l

    val messagePing = Message()
    messagePing.what = PING
    mHandlerThread.sendMessage(messagePing)
}
```

## Authorship and license
Igor Kim

The repository is distributed under [MIT license](LICENSE.MD).
