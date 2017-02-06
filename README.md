Bytes
=====

[![Build Status](https://travis-ci.org/avast/bytes.svg?branch=master)](https://travis-ci.org/avast/bytes)
[ ![Download](https://api.bintray.com/packages/avast/maven/bytes/images/download.svg) ](https://bintray.com/avast/maven/bytes/_latestVersion)


This library contains an immutable representation of sequence of bytes: `com.avast.bytes.Bytes`.

The goal is to provide single immutable representation of bytes that is not dependent on a third-party library (like `com.google.protobuf.ByteString`).
However, multiple implementations of the `Bytes` interface can be created using third-party libraries to take advantage of their benefits (`io.netty.buffer.ByteBuf` for example).
Implementations that require third-party dependencies will be separated to their own module, while the core module provides only the API and implementations
based on Java standard library.

The interface provides methods for reading encapsulated data including random and linear access (`Bytes.byteAt(int)` and `Bytes.newInputStream()` respectively).
You can also create a limited view of the bytes via `Bytes.view(int, int)` - with the same semantics as `String.substring(int, int)`.

There is no API for methods like `map` or `flatMap`.

Usage
-----
Parsing Google Protocol Buffer message:
Assume `NotificationData` is a class generated by `protoc`.
```
Bytes bytes = ...
NotificationData message = NotificationData.parseFrom(bytes.newInputStream());
```

Serializing Google Protocol Buffer message to `Bytes`:
```
NotificationData message = ...
Bytes.BuilderStream builder = ByteBufBytes.newBuilder(message.getSerializedSize());
message.writeTo(builder);
Bytes bytes = builder.toBytes();
```

JDK-based implementations
-------------------------
The `core` module contains two implementations:
  * `ByteArrayBytes` - backed by `byte[]`
  * `ByteBufferBytes` - backed by `java.nio.ByteBuffer`

```
ByteArrayBytes bab = ByteArrayBytes.copyFrom(new byte[]{0, 0, 7});

ByteBufferBytes bbb = ByteBufferBytes.copyFrom(ByteBuffer.allocate(1024)); // the most useless buffer ever
```

com.google.protobuf.ByteString wrapper
--------------------------------------
The `gpb` module provides `ByteStringBytes` that implements `Bytes` by wrapping `com.google.protobuf.ByteString`.

```
ByteStringBytes bsb = ByteStringBytes.wrap(ByteString.copyFromUtf8("foo"));
```
