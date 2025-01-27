---
title: clickCollectionEnabled
description: Learn how to configure Web SDK to tetermine if link click data is automatically collected.
---

# `clickCollectionEnabled`

The `clickCollectionEnabled` property is a boolean that determines if the Web SDK automatically collects link data. If you do not set this variable, its default value is `true` which means that link tracking data is automatically collected by default. Setting this property to `false` is valuable in cases where you prefer to track link data manually.

When `clickCollectionEnabled` is enabled, the following XDM elements automatically populate with data:

* `xdm.web.webInteraction.name`
* `xdm.web.webInteraction.type`
* `xdm.web.webInteraction.URL`

Internal links, download links, and exit links are all automatically tracked by default when this boolean is enabled. If you want more control over automatic link tracking, Adobe recommends using the [`clickCollection`](clickcollection.md) object.

## Automatic link tracking logic

The Web SDK tracks all clicks on `<a>` and `<area>` HTML elements if it doesn't have an `onClick` attribute. Clicks are captured with a [capture](https://www.w3.org/TR/uievents/#capture-phase) click event listener that is attached to the document. When a valid link is clicked, the following logic is run in order:

1. If the link matches criteria based on values in [`downloadLinkQualifier`](downloadlinkqualifier.md), or if the link contains a `download` HTML attribute, `xdm.web.webInteraction.type` is set to `"download"` (if `clickCollection.downloadLinkEnabled` is enabled).
1. If the link target domain differs from the current `window.location.hostname`, `xdm.web.webInteraction.type` is set to `"exit"` (if `clickCollection.exitLinkEnabled` is enabled).
1. If the link doesn't qualify for either `"download"` or `"exit"`, `xdm.web.webInteraction.type` is set to `"other"`.

In all cases, `xdm.web.webInteraction.name` is set to the link text label and `xdm.web.webInteraction.URL` is set to the link destination URL. If you want to set the link name to the URL as well, you can override this XDM field using the `filterClickDetails` callback in the `clickCollection` object.

## Enable automatic link tracking using the Web SDK tag extension {#tag-extension}

Select the **[!UICONTROL Enable click data collection]** checkbox when [configuring the tag extension](/help/tags/extensions/client/web-sdk/web-sdk-extension-configuration.md).

1. Log in to [experience.adobe.com](https://experience.adobe.com) using your Adobe ID credentials.
1. Navigate to **[!UICONTROL Data Collection]** > **[!UICONTROL Tags]**.
1. Select the desired tag property.
1. Navigate to **[!UICONTROL Extensions]**, then click **[!UICONTROL Configure]** on the [!UICONTROL Adobe Experience Platform Web SDK] card.
1. Scroll down to the [!UICONTROL Data Collection] section, then select the checkbox **[!UICONTROL Enable click data collection]**.
1. Click **[!UICONTROL Save]**, then publish your changes.

## Enable automatic link tracking using the Web SDK JavaScript library {#library}

Set the `clickCollectionEnabled` boolean when running the `configure` command. If you omit this property when configuring the Web SDK, it defaults to `true`. Set this value to `false` if you prefer to set `xdm.web.webInteraction.type` and `xdm.web.webInteraction.value` manually.

```js
alloy(configure, {
  edgeConfigId: "ebebf826-a01f-4458-8cec-ef61de241c93",
  orgId: "ADB3LETTERSANDNUMBERS@AdobeOrg",
  clickCollectionEnabled: false
});
```
