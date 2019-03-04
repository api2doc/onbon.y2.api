Onbon Y2 Java Library (preview version)
===
* [API Document](https://api2doc.github.io/onbon.y2.api/)
* [中文範例](README_TW.md)

## Introduction

The Onbon Y2 Java library provides an API for operating full-color board of Y2 Series.

Y2 Java library supports Android 5.0+ (API level 21+) and Java 6+.
## Jar Files
* __y2__ - Y2 core API
* __y2-message__ - Y2 messages
* __y2-Http__ - Y2 http interface
* __y2-http-hc__ - Y2 http implementation
  * httpmime
    * httpclient
      * httpcore
      * commons-logging
      * commons-codec
* __y2-http-ok__ - Y2 http implementation
  * okhttp
* __uia-utils__ - Common utilities
* __simple-xml__ - XML parser
  * stax-api
  * stax
  * xpp3
## Android only
* j2a - Java wrapper.

## Http Driver
There are two http drivers: __Apache HttpComponents__ and __OkHttp__. Default driver Y2 API uses is Apache HttpComponents.
* __Apache HttpComponents__

  The driver runs on __Java 6+__. _Not support Andorid_.

* __OkHttp__

  The driver runs on __Java 8__ only and supports __Android__.

## How To Use
1. Initialize API runtime environment. Just once.
    ```java
    // without log4j
    Y2Env.initial();

    // with log4j
    Y2Env.initial(pathToLog4j);

    // android
    Y2Env.initial(true);

    // with log4j and JDK 8
    Y2Env.initial(pathToLog4j, true);
    ```

2. Connect to a specific screen and run some commands.
    ```java
    // 1. new screen instance
    Y2Screen screen = new Y2Screen("http://1.2.3.4");

    // 2. login
    if (!screen.login(user, pwd)) {
        return;
    }

    // 3. do something
    screen.turnOn();
    screen.changeVolume(60);
    ...

    // 4. logout
    screen.logout();
    ```

## Toturial
### Simple Operations
```java
// 1
Y2Screen screen = new Y2Screen("http://1.2.3.4");

// 2
if (!screen.login("guest", "guest")) {
    return;
}

// 3
screen.syncTime(new Date());
screen.turnOff();
screen.turnOn();
screen.changeVolume(60);
screen.clearPlayResources();

// 4
screen.logout();
```

### Play a marquee
```java
// 1
Y2Screen screen = new Y2Screen("http://1.2.3.4");

// 2
if (!screen.login("guest", "guest")) {
    return;
}

// 3.1 create a marquee area
MarqueeArea area = new MarqueeArea(0, 0, screen.getWidth(), 40);
area.addContent("Hello everyone.")
    .fgColor(Color.green)
    .getFont()
        .size(16)
area.addContent("We are happy to announce that Y2 Java library has released.")
    .fgColor(Color.blue)
    .getFont()
        .size(20);


// 3.2 create a playable program
ProgramPlayFile prog = new ProgramPlayFile(1);
prog.getAreas().add(area);

// 3.3 write a playlist
String listId = screen.writePlaylist(playFile);

// 3.4 play
screen.play(listId);

// 4
screen.logout();
```

## Tutorial - Area and Program
### Marquee Area
The MarqueeArea is a single line text area which moves text horizontally from one side to another.
```java
MarqueeArea area = new MarqueeArea(0, 0, 128, 30);
area.right2Left(true);

// page1
area.addContent("Hello everyone.")
    .fgColor(Color.white)
    .bgColor(Color.black)
    .stuntSpeed(16)
    .getFont()
        .size(24)
        .bold()
        .strikethrough()
        .underline();

// page2
area.addContent("We are happy to announce that Y2 Java library has released.")
    .fgColor(Color.black)
    .bgColor(Color.white)
    .animationSpeed(1)
    .getFont()
        .size(20);

```

### Text Area
The TextArea is text based area which displays content section by section.
```java
TextArea area = new TextArea(0, 0, 128, 64);
area.stuntType(10);

// section1
area.addTextSection("Hello everyone.")
    .fgColor(Color.white)
    .bgColor(Color.black)
    .stayTime(8)
    .animationSpeed(16)
    .horizontalAlignment(AlignmentType.CENTER)
    .verticalAlignment(AlignmentType.CENTER)
    .rowHeight(30)
    .getFont()
        .size(24)
        .bold()
        .strikethrough()
        .underline();

// section2
area.addTextSection("We are happy to announce that Y2 Java library has released.")
    .fgColor(Color.black)
    .bgColor(Color.white)
    .stayTime(9)
    .animationSpeed(1)
    .horizontalAlignment(AlignmentType.NEAR)
    .verticalAlignment(AlignmentType.FAR)
    .rowHeight(20)
    .getFont()
        .size(20);

```

### DateTime Area
The DateTimeArea displays date and time with specific patterns. The DateTimeArea can be resized automatically according to content without values of the size.
```java
DateTimeArea area;

// x, y, width, height (fixed size)
area = new DateTimeArea(100, 40, 200, 60);
// x, y only (autosize)
area = new DateTimeArea(100, 40);

area.bgColor(Color.darkGray)
    .horizontalAlignment(AlignmentType.CENTER);

// line1: AM 8:16 2019-02-15
area.addUnits(DateTimePattern.AMPM_H_MM, DateTimePattern.YYYY_MM_DD1)
    .fgColor(Color.yellow);
    .getFont()
        .bold()
        .underline();

// line2: Friday
area.addUnits(DateTimePattern.WEEK);

// line3: February
area.addUnits(DateTimePattern.MONTH);
    .getFont()
        .bold();
```

### Bulletin Area
The bulletin area displays some important text based messages immediately.
```java
Y2BulletinManager bulletin = screen.bulletin();

BulletinArea area1 = new BulletinArea(1, "bullet_01", 0, 0, 100, 40);
area1.bgColor(Color.darkGry)
     .fgColor(Color.red)
     .content("News: We are happy to announce to release this API.")
BulletinArea area2 = new BulletinArea(2, "bullet_02", 0, 100, 100, 40);
area2.bgColor(Color.darkGry)
     .fgColor(Color.green)
     .content("News: Java Doc is available too.")

// write to Y2
bulletin.write(area1);
bulletin.write(area2);
// play
bulletin.play();
// delete
bulletin.delete(2);
// stop
bulletin.stop();

```

### Dynamic Area
The dynamic area displays some messages without any time configuration, and be deleted automatically after rebooting the hardware.
```java
Y2DynamicManager dyn = screen.dynamic();

DynamicPlayFile file = new DynamicPlayFile();

DynamicArea area = file.create(0, 0, 100, 40);
area.addText("Welcome to ONBON");
area.addText("We are happy to announce to release this API")

dyn.write(file);

```
