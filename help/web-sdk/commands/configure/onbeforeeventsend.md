---
title: onBeforeEventSend
description: Learn how to configure the Web SDK to register a JavaScript function that can alter the data you send just before that data is sent to Adobe.
---

# `onBeforeEventSend`

The `onBeforeEventSend` callback allows you to register a JavaScript function that can alter the data you send just before that data is sent to Adobe. This callback allows you to manipulate the `xdm` or `data` object, including the ability to add, edit, or remove elements. You can also conditionally cancel the sending of data altogether, such as with detected client-side bot traffic.

>[!WARNING]
>
>This callback allows the use of custom code. If any code that you include in the callback throws an uncaught exception, processing for the event halts. Data is not sent to Adobe.

## Configure on before event send callback using the Web SDK tag extension {#tag-extension}

Select the **[!UICONTROL Provide on before event send callback code]** button when [configuring the tag extension](/help/tags/extensions/client/web-sdk/web-sdk-extension-configuration.md). This button opens a modal window where you can insert the desired code.

1. Log in to [experience.adobe.com](https://experience.adobe.com) using your Adobe ID credentials.
1. Navigate to **[!UICONTROL Data Collection]** > **[!UICONTROL Tags]**.
1. Select the desired tag property.
1. Navigate to **[!UICONTROL Extensions]**, then click **[!UICONTROL Configure]** on the [!UICONTROL Adobe Experience Platform Web SDK] card.
1. Scroll down to the [!UICONTROL Data Collection] section, then select the button **[!UICONTROL Provide on before event send callback code]**.
1. This button opens a modal window with a code editor. Insert the desired code, then click **[!UICONTROL Save]** to close the modal window.
1. Click **[!UICONTROL Save]** under extension settings, then publish your changes.

Within the code editor, you have access to the following variables:

* **`content.xdm`**: The [XDM](../sendevent/xdm.md) payload for the event.
* **`content.data`**: The [data](../sendevent/data.md) object payload for the event.
* **`return true`**: Immediately exit the callback and send data to Adobe with the current values in the `content` object.
* **`return false`**: Immediately exit the callback and abort sending data to Adobe.

Any variables defined outside of `content` can be used, but are not included in the payload sent to Adobe.

```js
// Use nullish coalescing assignments to add objects if they don't yet exist
content.xdm.commerce ??= {};
content.xdm.commerce.order ??= {};

// Then add the purchase ID
content.xdm.commerce.order.purchaseID = "12345";

// Use optional chaining to prevent undefined errors when setting tracking code to lower case
if(content.xdm.marketing?.trackingCode) content.xdm.marketing.trackingCode = content.xdm.marketing.trackingCode.toLowerCase();

// Delete operating system version
if(content.xdm.environment) delete content.xdm.environment.operatingSystemVersion;

// Immediately end onBeforeEventSend logic and send the data to Adobe for this event type
if (content.xdm.eventType === "web.webInteraction.linkClicks") {
  return true;
}

// Cancel sending data if it is a known bot
if (myBotDetector.isABot()) {
  return false;
}
```

>[!TIP]
>Avoid returning `false` on the first event on a page. Returning `false` on the first event can negatively impact personalization.

## Configure on before event send callback using the Web SDK JavaScript library {#library}

Register the `onBeforeEventSend` callback when running the `configure` command. You can change the `content` variable name to any value that you would like by changing the parameter variable inside the inline function.

```js
alloy("configure", {
  edgeConfigId: "ebebf826-a01f-4458-8cec-ef61de241c93",
  orgId: "ADB3LETTERSANDNUMBERS@AdobeOrg",
  onBeforeEventSend: function(content) {
    // Use nullish coalescing assignments to add a new value
    content.xdm._experience ??= {};
    content.xdm._experience.analytics ??= {};
    content.xdm._experience.analytics.customDimensions ??= {};
    content.xdm._experience.analytics.customDimensions.eVars ??= {};
    content.xdm._experience.analytics.customDimensions.eVars.eVar1 = "Analytics custom value";
    
    // Use optional chaining to change an existing value
    if(content.xdm.web?.webPageDetails) content.xdm.web.webPageDetails.URL = content.xdm.web.webPageDetails.URL.toLowerCase();
    
    // Remove an existing value
    if(content.xdm.web?.webReferrer) delete content.xdm.web.webReferrer.URL;
    
    // Return true to immediately send data
    if (sendImmediate == true) {
      return true;
    }
    
    // Return false to immediately cancel sending data
    if(myBotDetector.isABot()){
      return false;
    }
    
    // Assign the value in the 'cid' query string to the tracking code XDM element
    content.xdm.marketing ??= {};
    content.xdm.marketing.trackingCode = new URLSearchParams(window.location.search).get('cid');
  }
});
```

You can also register your own function instead of an inline function.

```js
function lastChanceLogic(content) {
  content.xdm.application ??= {};
  content.xdm.application.name = "App name";
}

alloy("configure", {
  edgeConfigId: "ebebf826-a01f-4458-8cec-ef61de241c93",
  orgId: "ADB3LETTERSANDNUMBERS@AdobeOrg",
  onBeforeEventSend: lastChanceLogic
});    
```
