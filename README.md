# getAllScreensMedia()

### Introduction

Some companies run a managed device fleet. Some of them need a way to obtain video tracks of all screens, for such use cases as:
* Recording sessions for compliance or training reasons.
* Allowing a colleague to connect remotely and provide assistance.

The state of the art is that they need to call getDisplayMedia() multiple times, with a new transient activation each time, and instruct the user to select the "next" screen each time. This is cumbersome and error-prone. For such companies, an API that allows capturing all screens with one transient activation - or none - would be preferable. Such an API would usually be very risky and open to abuse. But in the context of managed-devices, the device can be configured to only allow the API to be invoked from a small set of allowlisted origins.

### Proposed Solution

Add a new method getAllScreensMedia that returns a promise to a sequence of media streams.

```WebIdl
partial interface MediaDevices {
  Promise<sequence<MediaStream>> getAllScreensMedia();
}
```
