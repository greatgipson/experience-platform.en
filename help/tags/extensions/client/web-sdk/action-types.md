---
title: Action Types in the Adobe Experience Platform Web SDK Extension
description: Learn about the different action types provided by the Adobe Experience Platform Web SDK tag extension.
solution: Experience Platform
exl-id: a4bf0bb9-59b4-4c43-97e6-387768176517
---

# Action types

After you configure the [Adobe Experience Platform Web SDK tag extension](web-sdk-extension-configuration.md), you must configure your action types.

This page describes the action types supported by the [Adobe Experience Platform Web SDK tag extension](web-sdk-extension-configuration.md).


## Apply response {#apply-response}

Use the **[!UICONTROL Apply response]** action type when you want to perform various actions based on a response from the Edge Network. This action type is typically used in hybrid deployments where the server makes an initial call to the Edge Network, then this action type takes the response from that call and initializes the Web SDK in the browser.

Using this action type may reduce client load times for hybrid personalization use cases.

![Image of the Experience Platform user interface showing the Apply response action type.](assets/apply-response.png)

This action type supports the following configuration options:

* **[!UICONTROL Instance]**: Select the Web SDK instance that you are using.
* **[!UICONTROL Response headers]**: Select the data element which returns an object containing the header keys and values returned from the Edge Network server call.
* **[!UICONTROL Response body]**: Select the data element which returns the object containing the JSON payload provided by the Edge Network response.
* **[!UICONTROL Render visual personalization decisions]**: Enable this option to automatically render the personalization content provided by the Edge Network and pre-hide the content to prevent flicker.

## Send event {#send-event}

Sends an event to Adobe [!DNL Experience Platform] so that Adobe Experience Platform can collect the data you send and act on that information. Select an instance (if you have more than one). Any data that you want to send can be sent in the **[!UICONTROL XDM Data]** field. Use a JSON object that conforms to the structure of your XDM schema. This object can either be created on your page or through a **[!UICONTROL Custom Code]** **[!UICONTROL Data Element]**.

There are a few other fields in the Send Event action type that could also be useful depending on your implementation. Please note that these fields are all optional.

* **Type:** This field allows you specify an event type that will be recorded in your XDM schema. See [`type`](/help/web-sdk/commands/sendevent/type.md) in the `sendEvent` command for more information.
* **Data:** Data that does not match an XDM schema can be sent using this field. This field is useful if you are trying to update an Adobe Target profile or send Target Recommendations attributes. See [`data`](/help/web-sdk/commands/sendevent/data.md) in the `sendEvent` command for more information.<!--- **Merge ID:** If you would like to specify a merge ID for your event, you can do so in this field. Please note that the solutions downstream are not able to merge your event data at this time. -->
* **Dataset ID:** If you need to send data to a dataset other than the one you specified in your datastream, you can specify that dataset ID here.
* **Document will unload:** If you would like to make sure that the events reach the server even if the user navigates away from the page, check the **[!UICONTROL Document will unload]** checkbox. This allows events to reach the server but responses are ignored.
* **Render visual personalization decisions:** If you want to render personalized content on your page, check the **[!UICONTROL Render visual personalization decisions]** checkbox. You can also specify decision scopes and/or surfaces if necessary. See the [personalization documentation](/help/web-sdk/personalization/rendering-personalization-content.md#automatically-rendering-content) for more information on rendering personalized content.

## Set consent {#set-consent}

After you have received consent from your user, this consent must be communicated to the Adobe Experience Platform Web SDK by using the "Set Consent" action type. Currently, two types of standards are supported: "Adobe" and "IAB TCF." See [Supporting Customer Consent Preferences](../../../../web-sdk/commands/setconsent.md). When using Adobe version 2.0, only a data element value is supported. You will need to create a data element that resolves to the consent object.

In this action, you are also provided with an optional field to include an Identity Map so that identities can be synced once consent is received. Syncing is useful when the consent is configured as "Pending" or "Out" because the consent call is likely the first call to fire.

## Update variable {#update-variable}

Use this action to modify an XDM object as a result of an event. This action is intended to build up an object that can later be referenced from a **[!UICONTROL Send event]** action, to record the event XDM object.

In order to use this action type you must have defined a [variable](data-element-types.md#variable) data element. Once you choose a variable data element to modify, an editor appears, similar to the editor for the [XDM object](data-element-types.md#xdm-object) data element.

![](assets/update-variable.png)

The XDM schema that is used for the editor is the schema that is selected on the [!UICONTROL variable] data element. You can set one or more properties of the object by clicking on one of the properties in the tree on the left, and then modifying the value on the right.For example, in the screenshot below, the producedBy property is getting set to the data element "Produced by data element."

![](assets/update-variable-set-property.png)

There are some differences between the editor in the update variable action versus the editor in the XDM object data element. First, the update variable action has a root level item labeled "xdm." If you click on this item, you can specify a data element to use to set the entire object. Second, the update variable action has checkboxes to clear the data from the xdm object. Click on one of the properties on the left, and then check the checkbox on the right to clear the value. This will clear out the current value before setting any values on the variable.

## Send media event {#send-media-event}

Sends a media event to Adobe Experience Platform and/or Adobe Analytics. This action is useful when you are tracking media events on your website. Select an instance (if you have more than one). The action requires a `playerId` that represents a unique identifier for a tracked media session. It also requires a **[!UICONTROL Quality of Experience]** and a `playhead` data element when starting a media session.

![Platform UI image showing the send media event screen.](assets/send-media-event.png)

The **[!UICONTROL Send media event]** action type supports the following properties:

* **[!UICONTROL Instance]**: The Web SDK instance that is being used.
* **[!UICONTROL Media Event Type]**: The type of the media event being tracked.
* **[!UICONTROL Player ID]**: The media session unique identifier.
* **[!UICONTROL Playhead]**: The current position of the media playback, in seconds.
* **[!UICONTROL Media session details]**: When sending a media start event, the required media session details should be specified.
* **[!UICONTROL Chapter details]**: In this section you can specify the chapter details when sending a chapter start media event.
* **[!UICONTROL Advertising details]**: When sending an `AdBreakStart` event, you must specify the required advertising details.
* **[!UICONTROL Advertising pod details]**: Details about the advertising pod when sending an `AdStart` event.
* **[!UICONTROL Error details]**: Details about the playback error that is being tracked.
* **[!UICONTROL State Update Details]**: The player state that is being updated.
* **[!UICONTROL Custom Metadata]**: The custom metadata about the media event that is being tracked.
* **[!UICONTROL Quality of Experience]**: The media quality of experience data that is being tracked.

## Get Media Analytics Tracker {#get-media-analytics-tracker}

This action is used to get the legacy Media Analytics API. When configuring the action and an object name is provided, then the legacy Media Analytics API will be exported to that window object. If none is provided it will be exported to `window.Media` as the current Media JS library does.

![Platform UI image showing the Get Media Analytics Tracker action type.](assets/get-media-analytics-tracker.png)

## Redirect with identity {#redirect-with-identity}

Use this action type to share identities from the current page to other domains. This action is designed to be used with a **[!UICONTROL click]** event type and a value comparison condition. See [append identity to URL using the Web SDK extension](../../../../web-sdk/commands/appendidentitytourl.md#extension) for more information on how to use this action type.

## Next steps {#next-steps}

After reading this article, you should have a better understanding of how to configure your actions. Next, read about how to [configure your data element types](data-element-types.md).
