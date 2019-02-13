Onbon Y2 API
===
## Introduction

The Onbon Y2 Java library provides an API for operating full color card of Y2 Series.

Y2 Java API supports Android 5.0+ (API level 21+) and Java 6+.

## Http Driver
There are two http drivers Y2 uses:
* __Apache HttpComponents__

    Use this driver on __Java 6+__. _Not support Andorid_.

* __OkHttp__

    Use this driver if your application runs on the __Android__ or __Java 8__.

Default driver Y2 API uses is Apache HttpComponents. You can change to OkHttp by
```Java
// OkHttp
Y2HttpClient.driver = "onbon.y2.http.ok.SimpleHttpClient";
```

## Toturial
Enable Y2 API in runtime environment.
```java
// without log4j
Y2Env.initial();

// with log4j
Y2Env.initial(pathToLog4j);
```
Change http driver to OkHttp if your application runs on __Android__.
```Java
Y2HttpClient.driver = "onbon.y2.http.ok.SimpleHttpClient";
```

### Simple Operations
```java
Y2Screen screen = new Y2Screen();
if (!screen.login("guest", "guest")) {
    return;
}

screen.syncTime(new Date());
screen.turnOff();
screen.turnOn();
screen.changeVolume(60);
screen.clearPlayResources();

screen.logout();
```

### Play Marquee
```java
Y2Screen screen = new Y2Screen();
if (!screen.login("guest", "guest")) {
    return;
}

// 1. marquee area
MarqueeArea area = new MarqueeArea(0, 0, screen.getWidth(), 40);
area.addContent("Hello everyone.")
    .fgColor(Color.green)
    .getFont().size(16)
area.addContent("We are happy to announce Y2 Java API has released.")
    .fgColor(Color.blue)
    .getFont().size(20);


// 2. program
ProgramPlayFile prog = new ProgramPlayFile(1);
prog.getAreas().add(area);

// 3. playlist
String listId = screen.writePlayList(playFile);

// 4. play
screen.play(listId);

screen.logout();
```
