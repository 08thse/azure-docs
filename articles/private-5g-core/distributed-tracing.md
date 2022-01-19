---
title: Distributed tracing for Azure Private 5G Core Preview
description: Information on the distributed tracing web GUI, which provides detailed traces for signaling flows involving packet core instances.
author: djrmetaswitch
ms.author: drichards
ms.service: private-5g-core
ms.topic: conceptual
ms.date: 12/23/2021
ms.custom: template-concept
---

# Distributed tracing

Azure Private 5G Core Preview offers a distributed tracing web GUI which you can use to collect detailed traces for signaling flows involving packet core instances. These can be used to diagnose many common configuration, network and interoperability problems affecting user service.

## Searching for specific information

The distributed tracing web GUI provides the following search tabs to allow you to search for diagnostics.

If you cannot see the **Search** heading, click on the **Search** button in the top-level menu.

- **SUPI** - Allows you to search for activity involving a particular subscriber using their Subscription Permanent Identifier (SUPI). This tab also provides an Errors panel which allows you to filter the results by error condition. To search for activity for a particular subscriber, enter all of the initial digits of the subscriber's SUPI into the text box on the **SUPI search** panel.
- **Errors** - Allows you to search for error condition occurrences across all subscribers. To search for occurrences of error conditions across all subscribers, select the **Errors** tab and then use the drop down menus on the **Error** panel to select an error category and, optionally, a specific error.

  :::image type="content" source="media\distributed-tracing\distributed-tracing-search-display.png" alt-text="Search display in the distributed tracing web GUI, showing the SUPI and Errors tabs.":::

Both tabs also provide a **Date/time range** panel that allows you to specify a custom time window in which to search for diagnostics data. You can specify this time window in several ways.

- Select **Most recent** and choose an option to search for records from the most recent 15 minutes, 30 minutes, 1 hour or 2 hours.
- Select **Today**, **Yesterday** or a specific date, then select an hour-long range on the specified date by clicking on the ribbon.
- Select **Custom range**, then specify the dates and times for the start and end of the search period. This allows you to specify a search period that spans consecutive days.

Long search ranges result in slower searches, so it is recommended that you keep the search range to an hour or less if possible.

> [!TIP] You can click the cog icon next to the **Date/time range** heading to customize the date and time format, default search period, and time zone according to your preferences.

Once you have entered your chosen search parameters, click **Search** to begin your search. The following image shows an example of the results returned for a search on a particular SUPI.

:::image type="content" source="media\distributed-tracing\distributed-tracing-search-results.png" alt-text="A number of matching Successful PDU Session Establishment records displayed in the distributed tracing web GUI after a search on a specific SUPI.":::

You can view more information on any result by clicking on it.

## Viewing diagnostics details

When you click on a specific result, the display shows the following tabs containing different categories of information.

> [!NOTE] In addition to the tabs described below, the distributed tracing web GUI also includes a **User Experience** tab. This tab is not used by Azure Private 5G Core Preview and will not display any information.

The **Summary** view displays a description of the flow or error.

:::image type="content" source="media\distributed-tracing\distributed-tracing-summary-view.png" alt-text="The Summary view of the distributed tracing web GUI, providing detailed information on a Successful PDU Session Establishment record.":::

The **Detailed Timeline** view shows the sequence of operations and events that occurred during the course of the flow or error.

:::image type="content" source="media\distributed-tracing\distributed-tracing-detailed-timeline.png" alt-text="The Detailed Timeline view of the distributed tracing web GUI, providing a list of messages sent between Network Functions and other components.":::

Each entry in the list shows summary information for a specific event that occurred during the flow or error. This includes the date and time at which it occurred and the name of the component on which it occurred. When you select a specific entry in this list, the panel at the bottom of the screen provides more detail about the selected event.

The **Events to be viewed** drop-down list allows you to control the level of events that are included in the list. You can choose from the following levels.

- **High level events** - the lowest level of detail, with a one-line summary of each event.
- **High level events and protocol flows** - this includes the same information as for **High level events**, but adds details of the contents of network protocol messages involved at each stage.
- **Detailed events** - this includes the network protocol messages and more fine-grained detail of events.
- **Engineer level events** - this provides a very detailed listing of internal events, typically for use by Microsoft personnel.

The **Call Flow** view shows the sequence of messages flowing between components during the course of the flow or error.

:::image type="content" source="media\distributed-tracing\distributed-tracing-call-flow.png" alt-text="The Call Flow view of the distributed tracing web GUI, showing the flow of messages exchanged during a Successful PDU Session Establishment.":::

The vertical lines in the diagram show the network components involved in the flow.

- Black lines indicate packet core Network Functions that have logged sending or receiving messages for this flow.
- Grey lines indicate other components that do not log messages.

You can customize the view by showing or hiding individual columns and giving them more descriptive display names. To view these options, click on the current column name and then click the **+** (plus) sign that appears to the right of it to open a dropdown menu. Additionally, you can select multiple columns by holding down the Ctrl key as you click on each column; the **+** symbol remains next to the latest column that you selected.

- The **Remove this column** option hides the currently select column from view.
- The **Remove other columns** option hides all columns that do not include messages flowing to or from the selected column.
- The **Group columns** option allows you to combine several columns into a single column.
- The **Ungroup columns** option allows you to revert the **Group columns** option.
- The **Show messages within group** option shows any messages between group members as arrows that loop back on themselves to their originating column.
- The **Set annotation** option allows you to enter a new display name for the column.

You can revert to the default display options using the **Options** menu (click on a white cogwheel on a blue background at the top-right-hand corner of the view window). Choose **Colors, styles and annotations -> Revert to default** to clear your cutom display names, choose **Visibility -> Show all** to restore columns you have previously hidden from view, and choose **Column grouping -> Ungroup all** to separate columns you have previously grouped.

A horizontal line in the diagram shows each individual signaling message flowing between two network components, with an arrow indicating the direction of flow from the sending to the receiving component.

- A double line indicates that the message was logged by both the sending and receiving components.
- A single line indicates that the message was logged by only one of these components, because the other component does not log messages.
- A line that is half double and half single, with an **X** symbol at the midpoint, indicates one of the following.
  - The message should have been logged by both components but was logged by only one of them. For example, this occurs if a message is logged by the sending component but is then lost in transit and never reaches the receiving component. 
  - The message crossed with another message in the diagram while in transit, and so was received out of order.
  - The messages were logged in the wrong order. This does not indicate a problem with your deployment; it can happen because of network latency in communications.
- A retransmitted message appears as a separate line for each retransmission.
- A looped line that returns to the same column indicates a message between group members.

Different colors and line styles (dashed, dotted, etc.) for horizontal lines are used to distinguish between different call legs.

The messages appear in the diagram in the order in which they occurred. An axis break on all of the vertical lines in the diagram between two consecutive messages indicates that a gap of 10 seconds or more occurred between these two messages.

If the call flow diagram is too large to fit in the browser window, you can use the vertical and horizontal scrollbars to mve around the display.

To view help information, click on the **Options** symbol in the top right corner and choose **Help**. The help information appears in a panel at the bottom of the display. To hide this panel, click on the **X** symbol at the top right corner of the panel.

## Next steps

[Learn more about how you can monitor your deployment using the packet core dashboards](packet-core-dashboards.md)