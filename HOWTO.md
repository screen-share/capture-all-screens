---
title: "How to experiment with getAllScreensMedia in Chrome"
maintainer: "simonha"
created: 2023/05/05
updated: 2023/06/07
---

# How to experiment with getAllScreensMedia on ChromeOS

A prototype of the API is present in ChromeOS 116. **This is intended for experimentation purposes and is subject to change as a specification takes shape and develops.**

We will do our best to keep these instructions up to date as protocol and API changes occur.

## Enabling getAllScreensMedia
You need to enable the API through one of the two following ways:

### Enabling getAllScreensMedia on ChromeOS via feature flag
1. Download a version of Chrome with `getAllScreensMedia` implemented. ([Link for Canary channel](https://www.google.com/chrome/))
2. Launch Chrome and open the [chrome://flags](chrome://flags) page.
3. Search for `GetAllScreensMedia API` and set the flag to enabled.
4. Restart the browser.

### Enabling getAllScreensMedia on ChromeOS via origin trial
1. Download a version of Chrome with `getAllScreensMedia` implemented. ([Link for Canary channel](https://www.google.com/chrome/))
2. Register your page for [origin trial](https://developer.chrome.com/origintrials/#/view_trial/3079336245214576641)
3. Embed the origin trial token on your page (see [instructions](https://googlechrome.github.io/OriginTrials/developer-guide.html))

## Set up the managed allow-list for your page
1. Contact your partner engineer to set the admin-managed allowlist for `getAllScreensMedia`
2. Wait for the policy value to propagate to your device. ([Link to the policy page on Chrome](chrome://policy))
3. Restart your system (*Note: this is by design and changes only come into effect after a restart to improve privacy.*)
4. Navigate to the allow-listed page and use `getAllScreensMedia`

## Page requirements
To improve security, we ask users of this API to configure a strict CSP and trusted types on their page. getAllScreensMedia will not be visible if this requirement is not met.

## Examples

### Embedding your origin trial token
```html
<meta http-equiv="origin-trial" content="YOUR_ORIGIN_TRIAL_TOKEN">
```

### Strict CSP and trusted types
```html
<meta http-equiv="Content-Security-Policy"
            content="object-src 'none'; base-uri 'none'; script-src 'strict-dynamic' 'YOUR_IMPORTED_SCRIPT_HASH';
            require-trusted-types-for 'script';trusted-types forceInner;" />
```

### getAllScreensMedia usage example
```javascript
async function captureAndShowAllScreens() {
  try {
    const screensDiv = document.getElementById("all-screens");
    const screens = await navigator.mediaDevices.getAllScreensMedia();
    screens.forEach((screen, index) => {
      const screenDiv = document.createElement("div");
      const metadata = document.createElement("div");
      const videoTrack = screen.getVideoTracks()[0];
      const screenDetailed = videoTrack.screenDetailed();
      metadata.textContent = `Screen ${index}: Position (${screenDetailed.top}, ${screenDetailed.left})`;
      screenDiv.appendChild(metadata);
      const screenElement = document.createElement("video");
      screenElement.width = 256;
      screenElement.height = 192;
      screenElement.autoplay = true;
      screenElement.srcObject = screen;
      screenDiv.appendChild(screenElement);
      screensDiv.appendChild(screenDiv);
    });
  } catch (e) {
    console.log("Unable to acquire screen captures: " + e);
  }
}
```
