# Capture all screens explainer

## Introduction
`getAllScreensMedia` is an API that allows clients to capture all monitors
attached to a device at once without user interaction. It is only available in
managed sessions for allow-listed web apps and it is a requirement
that the user must be informed about capturing at all times. The usage
indicator cannot be prevented by the web app.

## Motivation
Web developers have [expressed interest](https://github.com/w3c/mediacapture-screen-share/issues/204) for such an API in order to meet legal and internal
compliance requirements.

## Use cases
* Contact centers may require full documentation of provided information for
compliance and / or training purposes.
* In the financial industry a consultant may provide financial advice digitally
and a complete documentation of the information may be required by law in some
jurisdictions.
* Internet usage in prisons may require full traceability to allow convicts to
access the internet.

## API

`getAllScreensMedia` can be used similarly to `getDisplayMedia` with
a few differences:
* `getAllScreensMedia` returns a promise to a sequence of
`MediaStreams` (one `MediaStream` per monitor).
* Constraints cannot be passed in the `getAllScreensMedia` call. Constraints may be
different depending on the monitor and information determining the desired
constraints (resolution, size, …) is likely only available after the monitor
was captured. Constraints can be applied on the returned `MediaStreamTrack`
with `applyConstraints`.
* `getAllScreensMedia` will only be available for web apps allowlisted by a
policy.
* Usage indicators must be shown to the user at all times.

`getAllScreensMedia` will use `ScreenCaptureMediaStreamTrack` (which is a
subclass of `MediaStreamTrack`) in the returned `MediaStream` which provides
access to monitor details analogous to the
[getScreenDetails API](https://developer.chrome.com/articles/multi-screen-window-placement/#the-getscreendetails()-method).

```idl
partial interface MediaDevices {
 Promise<sequence<MediaStream>> getAllScreensMedia();
}
```

```idl
interface ScreenCaptureMediaStreamTrack : MediaStreamTrack {
  ScreenDetailed screenDetailed();
}
```

## Example usage
```js
try {
  const mediaStreams = await navigator.mediaDevices.getAllScreensMedia();
  mediaStreams.forEach((mediaStream, index) => {
    files.push(saveToFile(mediaStream));
  })
} catch (e) {
  console.log('Unable to acquire screen captures: ' + e);
}
```