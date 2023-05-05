---
title: "How to experiment with getAllScreensMedia in Chrome"
maintainer: "simonha"
created: 2023/05/05
updated: 2023/05/05
---

# How to experiment with getAllScreensMedia on ChromeOS

A prototype of the API is present in ChromeOS 116. **This is intended for experimentation purposes and is subject to change as a specification takes shape and develops.**

We will do our best to keep these instructions up to date as protocol and API changes occur.

## Enabling getAllScreensMedia on ChromeOS
1. Download a version of Chrome with `getAllScreensMedia` implemented. ([Link for Canary channel](https://www.google.com/chrome/))
2. Launch Chrome and open the [chrome://flags](chrome://flags) page.
3. Search for `GetAllScreensMedia API` and set the flag to enabled.
4. Restart the browser.
5. Navigate to the [Chrome Admin panel](https://admin.google.com) and login with your admin account.
6. Navigate to `Devices` > `Chrome` > `Settings` > `Users & browsers` and add the origin of the web page using `getAllScreensMedia` to the *Auto-select for multi screen captures* allow-list.
7. Wait for the policy value to propagate to your device. ([Link to the policy page](chrome://policy))
8. Navigate to the allow-listed page and use `getAllScreensMedia`.

## Example
```javascript
async function captureAndShowAllScreens() {
  try {
    const screensDiv = document.getElementById("all_screens");
    const screens = await navigator.mediaDevices.getAllScreensMedia();
    let screenNumber = 0;
    screens.forEach(async function(screen) {
      const screenDiv = document.createElement("div");
      const metadata = document.createElement("div");
      const videoTrack = await screen.getVideoTracks()[0];
      const screenDetailed = await videoTrack.screenDetailed();
      metadata.innerHTML = "Screen " + screenNumber + ": Position (" + screenDetailed.left + ", " + screenDetailed.top + ")";
      screenDiv.appendChild(metadata);
      const screenElement = document.createElement("video");
      screenElement.style.width = "256px";
      screenElement.style.height = "192px";
      screenElement.setAttribute("autoplay", "");
      screenElement.srcObject = screen;
      screenDiv.appendChild(screenElement);
      screensDiv.appendChild(screenDiv);
      screenNumber += 1;
    });
  } catch (e) {
    console.log('Unable to acquire screen captures: ' + e);
}
```