---
title: Revenue dashboard in commercial marketplace analytics | Azure Marketplace
description: The Revenue dashboard shows the summary of your billed sales of all offer purchases and consumption through the Microsoft commercial marketplace. 
ms.service: marketplace 
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 11/06/2021
---

# Revenue dashboard in commercial marketplace analytics

This article provides information on the Revenue dashboard in Microsoft Partner Center. The Revenue dashboard shows the summary of billed sales of all offer purchases and consumption through the commercial marketplace. Use this report to understand your revenue information across customers, billing models, offer plans, and so on. It provides a unified view across entities and helps answer queries, such as:

- How much revenue was invoiced to customers and when can I expect payouts?
- Which customer transacted the offer and where are they located?
- Which offer was purchased?
- When was the offer purchased or consumed?
- What are the billing models and sales channels used?

This article explains how to access the revenue report, understand the purpose of the various widgets on the page, and how to download the exported revenue reports.

## Revenue Dashboard

The [Revenue dashboard](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/revenue) displays the estimated revenue for all your order purchases and offer consumption. You can view graphical representations of the following items:

- Estimated revenue
- Transactions
- Estimated revenue timeline
- Customer leader board
- Geographical spread
- Details

## Access the Revenue dashboard

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### [Workspaces view](#tab/workspaces-view)

1. Sign in to [Partner Center](https://partner.microsoft.com/dashboard/home).

1. On the Home page, select the **Insights** tile.

    [ ![Illustrates the Insights tile on the Partner Center Home page.](./media/workspaces/partner-center-insights-tile.png) ](./media/workspaces/partner-center-insights-tile.png#lightbox)

1. In the left menu, select **Revenue**.

#### [Current view](#tab/current-view)

1. Sign in to [Partner Center](https://partner.microsoft.com/dashboard/home).

1. In the [Commercial Marketplace dashboard](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) in Partner Center, expand the **[Analyze](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** section and select **Revenue**.

    :::image type="content" source="./media/revenue-dashboard/revenue-dashboard-nav.png" alt-text="Illustrates the Revenue dashboard link in the left nav of the Partner Center Home page.":::

---

## Elements of the Revenue dashboard

The following sections describe how to use the Revenue dashboard and how to read the data.

### Month range

You can find a month range selection at the top-right corner of each page. Customize the output of the Revenue dashboard graphs by selecting a month range based on the past 3, 6 or 12 months, or by selecting a custom month range with a maximum duration of 12 months. The default month range (computation period) is six months. Month range selection is not applicable for Estimated revenue timeline widget.

:::image type="content" source="./media/revenue-dashboard/month-range-filter.png" alt-text="Illustrates the month range filter on the revenue dashboard.":::

> [!NOTE]
> Data for revenue report is available from Jan,2021 onwards.

### Payout currency selector

You can choose to view the revenue figures in either USD or your preferred pay out currency.

:::image type="content" source="./media/revenue-dashboard/payout-currency-selector.png" alt-text="Illustrates the payout currency selector on the revenue dashboard.":::

### Dashboard filters

The page has different dashboard-level filters you can use to filter the Revenue data based on offer type, offer name, billing model, sales channel, payment instrument type, payout status, and estimated payout month. Each filter is expandable with multiple options that you can select. Filter options are dynamic and based on the selected date range.

> [!NOTE]
> Filters are not applicable for the Estimated revenue timeline widget.

:::image type="content" source="./media/revenue-dashboard/dashboard-filters.png" alt-text="Illustrates the filters on a revenue dashboard widget.":::

### Estimated revenue

In this section, you will find the _estimated revenue_ information which shows the overall billed sales of a partner for the selected date range and page filters.

The _Total revenue_ represents the billed sales of pay-outs or earnings mapped to different pay out statuses – _sent_, _upcoming_ and _unprocessed_.

The _Others revenue_ represents billed sales with earnings that either are rejected, reprocessed, not eligible, uncollected from the customers, or not reconcilable with transaction amounts in the earnings report.

> [!NOTE]
> There are no earnings entries in the transaction history report for estimated revenue figures with the rejected, reprocessed, not eligible, or not reconcilable status. This screenshot shows an example of an unprocessed billed sale.

[ ![Illustrates the estimated revenue section of the Revenue dashboard.](./media/revenue-dashboard/estimated-revenue.png) ](./media/revenue-dashboard/estimated-revenue.png#lightbox)

### Transactions

In this section, you will find the _transactions information_ that shows the overall count of the order purchases or offer consumption for a partner for the selected date range and page filters.

Each transaction represents a unique combination of purchase record ID and line-item ID in the revenue report. Transaction information is further categorized based on orders (subscriptions) and consumption (usage) based billing models.

[ ![Illustrates the Transactions section of the Revenue dashboard.](./media/revenue-dashboard/transactions-widget.png) ](./media/revenue-dashboard/transactions-widget.png#lightbox)

### Estimated revenue timeline

In this section, you will find the _estimated revenue timeline_ information that displays the last payment date and the revenue figures of upcoming payments and their associated timelines. The upcoming revenue values shown are figures based on the current date.

[ ![Illustrates the Estimated revenue timeline section of the Revenue dashboard.](./media/revenue-dashboard/estimated-revenue-timeline.png) ](./media/revenue-dashboard/estimated-revenue-timeline.png#lightbox)

### Customer leader board

In this section, you will find the information for top customers who contribute the most to estimated revenue. All figures are reported in the partner preferred currency and can be sorted on different columns. You can select each row of the table and see the corresponding revenue split across different statuses, and the revenue trend for the selected month range.

[ ![Illustrates the Customer leader board section of the Revenue dashboard.](./media/revenue-dashboard/customer-leader-board-1.png) ](./media/revenue-dashboard/customer-leader-board-1.png#lightbox)

[ ![Illustrates the other half of the Customer leader board section of the Revenue dashboard.](./media/revenue-dashboard/customer-leader-board-2.png) ](./media/revenue-dashboard/customer-leader-board-2.png#lightbox)

### Geographical spread

In this section, you will find the geographic spread the total estimated revenue, estimated revenue for sent, upcoming and unprocessed pay out statuses. You can sort the table on different statuses. Total estimated revenue includes revenue for other statuses as well.

The light-to-dark colors on the map represent the low to high value of the total revenue. Select a record in the table to zoom in on a specific country or region.

[ ![Illustrates the geographical spread section of the Revenue dashboard.](./media/revenue-dashboard/geographical-spread-1.png) ](./media/revenue-dashboard/geographical-spread-1.png#lightbox)

[ ![Illustrates the other half of the geographical spread section of the Revenue dashboard.](./media/revenue-dashboard/geographical-spread-2.png) ](./media/revenue-dashboard/geographical-spread-2.png#lightbox)

Note the following:

- You can move around the map to view an exact location.
- You can zoom into a specific location.
- The heatmap has a supplementary grid to view the details of country or region name, total revenue, estimated revenue of sent, unprocessed, and upcoming earnings.
- You can search and select a country/region in the grid to zoom to the location in the map. To revert to the original view, select the **Home** button in the map.

### Details

The _Revenue details_ table displays a numbered list of the 1,000 top orders sorted by transaction month.

- Each column in the grid is sortable.
- The data can be extracted to a .CSV or .TSV file if the count of the records is less than 1,000. To download the report, select **Download raw data** (down arrow icon) in the upper-right of the widget.
- If records number over 1,000, exported data will be asynchronously placed in a downloads page for the next 30 days.
- Apply filters to the revenue details table to display only the data you're interested in. You can filter by order type, offer name, billing model, sales channel, payment instrument type, pay out status, and estimated pay out instrument.

[ ![Illustrates the Revenue details section of the Revenue dashboard.](./media/revenue-dashboard/details-widget.png) ](./media/revenue-dashboard/details-widget.png#lightbox)

Note the following:

- The revenue is an estimate since it factors the exchange currency rates. It is displayed in transaction currency, US dollar, or partner preferred currency. Values are displayed as per the selected date range and page filters.
- Estimated revenue is tagged with different statuses as explained in the [data dictionary table](#data-dictionary-table).
- Each row in the details section has estimated revenue which is an aggregate of all revenue figures for a unique combination of purchase record ID and line-item ID.
- Columns for customer attributes may contain empty values.

### Providing feedback

In the lower left of most widgets, you’ll see a thumbs up and thumbs down icon. Selecting the thumbs down icon displays this dialog box that you can use to submit your feedback on the widget.

[ ![Illustrates the feedback dialog box available from most widgets on the Revenue dashboard.](./media/revenue-dashboard/feedback-widget.png) ](./media/revenue-dashboard/feedback-widget.png#lightbox)

## Data dictionary table

| Data field | Definition |
|----|---------|
|<img width=200/>|<img width=500/>|
| Estimated revenue | Represents billed sales of a partner for customer’s offer purchases and consumption through the commercial marketplace. This is in transaction currency and will always be present in download reports. |
| Estimated revenue (USD) | Estimated revenue reported in US dollars. This column will always be present in download reports. |
| Estimated revenue (PC) | Estimated revenue reported in partner preferred currency. This column will always be present in download reports. |
| Status - Sent | The overall revenue for which earnings were sent to the Partner. |
| Status - Unprocessed | The overall revenue for which earnings are yet to be processed. This can occur for multiple reasons:<ul><li>Customer has made payment but the earnings to partner is not yet posted.</li><li>Manual payment cancellations or failed payment submissions, and so on.</li></ul> |
| Status - Upcoming | The overall revenue for which earnings are upcoming or pending. Revenue may be shown across months due to payment schedules and processes. |
| Status - Rejected | The overall revenue for which payments or safe approval were rejected. |
| Status - Not eligible | The overall revenue for which a partner is not eligible to receive pay outs. [Learn more](/partner-center/payout-statement) about eligibility. |
| Status - Reprocessed | The overall revenue under reprocessing due to various reasons such as invoice cancelation or safe approval cancelation, and so on. |
| Status - Unreconciled | The overall revenue for which successful reconciliation with earnings could not happen. This can occur for multiple reasons:<ul><li>Estimated revenue is generated but earnings are not yet posted</li><li>Some issues with software systems</li></ul> |
| Status - Uncollected | The overall revenue for which an end customer has not yet paid or has defaulted. [Learn more](/partner-center/payout-policy-details#process-for-customer-non-payment) about write-offs. For enterprise agreement (EA) customers there may be entries and for non-EA customers there will be no entries in the transaction history report. |
| Transactions | An order purchase or a usage event for which a purchase order id and line-item id is generated in the customer invoice. |
| Purchase record Id | Relates to a customer's invoice. Same as `order id` in the transaction history report. |
| Line-item Id | Individual line in a customer's invoice. Same as  `lineItemId` in the transaction history report. |
| Customer name | Name of the customer |
| Customer company name | Name of the customer’s company |
| Customer Id | The unique identifier assigned to a customer. A customer may have zero or more Azure Marketplace subscriptions. Same as `customer id` in the customers report. |
| Billing account Id | Identifier for the billing account of the customer. Same as customer id in the transaction history report. |
| Asset Id | An identifier for the software assets. Same as the `Order id` in the orders report in Partner Center. |
| Offer type | Type of offer, such as SaaS, VM, and so on. |
| Offer name | Display name of the offer |
| Offer plan | Specific offer plan, also referred to as SKU |
| Transaction month | The month for which the purchase record id and line-item id were generated |
| Transaction amount | Transaction amount in the original transaction currency. Refers to the transaction amount column in the transaction history report |
| Transaction currency | The customer currency used for a transaction |
| Transaction amount (USD) | Transaction amount in US dollars. Refers to the transaction currency USD column in the transaction history report |
| Transaction exchange rate (USD) | The exchange rate used to convert transaction amount and transaction amount in USD |
| Transaction amount (PC) | Transaction amount in Partner preferred currency. Refers to the transaction currency USD column in the transaction history report |
| Exchange rate (PC) | The exchange rate between Partner preferred currency and USD |
| Exchange rate date | The date used to calculate exchange rates for currency conversions |
| Estimated pay out month | The month for receiving your estimated earnings |
| Sales channel | Represents the sales channel for the customer. It is the same as Azure license type in the orders report and usage report. The possible values are:<ul><li>Cloud Solution Provider (CSP)</li><li>Enterprise (EA)</li><li>Enterprise through Reseller</li><li>Pay as You Go</li><li>Go to market (GTM)</li></ul> |
| Plan Id | Unique identifier for the plan in the offer |
| Product Id | Unique identifier for the offer in the product catalog |
| Billing model | Subscription or consumption-based billing model used for calculation of estimated revenue. It can have one of these two values:<ul><li>UsageBased</li><li>SubscriptionBased</li></ul> |
| Customer country | The country/region name provided by the customer. Country/region could be different than the country/region in a customer's Azure subscription. |
| Customer company | The company name provided by the customer |
| Customer email | The e-mail address provided by the end customer. This address could be different than the e-mail address in a customer's Azure subscription. |
| Payout currency | The partner preferred currency to receive pay-out. This is the same as the _lastpaymentcurrency_ column in the transaction history report. |
| Payment sent date | The date on which payment was sent to the partner |
| Quantity | Indicates billed quantity for transactions. This can represent the seats and site purchase count for subscription-based offers, and usage units for consumption-based offers. |
| Units | The unit quantity. Represents count of purchased seat/site SaaS orders and core hours for VM based offers. Units will be displayed as NA for offers with custom meters. |
---
