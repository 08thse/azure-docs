---
title: TBD | Microsoft Docs
description: TBD
author: limwainstein
ms.author: lwainstein
ms.topic: how-to
ms.date: 05/03/2022
ms.custom: ignite-fall-2021
---

# Migrate ArcSight detection rules

## Identify rules

Mapping detection rules from your SIEM to map to Microsoft Sentinel rules is critical. 
- Understand Microsoft Sentinel rules. Azure Sentinel has four built-in rule types:
    - Alert grouping: Reduces alert fatigue by grouping up to 150 alerts within a given timeframe, using three [alert grouping](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-reduce-alert-noise-with-incident-settings-and-alert/ba-p/1187940) options: matching entities, alerts triggered by the scheduled rule, and matches of specific entities.
    - Entity mapping: Enables your SecOps engineers to define entities to be tracked during the investigation. [Entity mapping](map-data-fields-to-entities.md) also makes it possible for analysts to take advantage of the intuitive [investigation graph](tutorial-investigate-cases.md) to reduce time and effort.
    - Evidence summary: Surfaces events, alerts, and bookmarks associated with a particular incident within the preview pane. Entities and tactics also show up in the incident pane—providing a snapshot of essential details and enabling faster triage.
    - KQL: The request is sent to a Log Analytics database and is stated in plain text, using a data-flow model that makes the syntax easy to read, author, and automate. Because several other Microsoft services also store data in [Azure Log](../azure-monitor/logs/log-analytics-tutorial.md) Analytics or [Azure Data Explorer](https://azure.microsoft.com/en-us/services/data-explorer/), this reduces the learning curve needed to query or correlate.
- Check you understand rule terminology using the diagram below.
- Don’t migrate all rules without consideration. Focus on quality, not quantity.
- Leverage existing functionality, and check whether Microsoft Sentinel’s [built-in analytics rules](detect-threats-built-in.md) might address your current use cases. Because Microsoft Sentinel uses machine learning analytics to produce high-fidelity and actionable incidents, it’s likely that some of your existing detections won’t be required anymore.
- Confirm connected data sources and review your data connection methods. Revisit data collection conversations to ensure data depth and breadth across the use cases you plan to detect.
- Explore community resources such as [SOC Prime Threat Detection Marketplace](https://my.socprime.com/tdm/) to check whether  your rules are available.
- Consider whether an online query converter such as Uncoder.io conversion tool might work for your rules? 
- If rules aren’t available or can’t be converted, they need to be created manually, using a KQL query. Review the [Splunk to Kusto Query Language map](../data-explorer/kusto/query/splunk-cheat-sheet.md).

## Compare rule terminology (pulled in from blog)

This diagram helps you to clarify the concept of a rule in Microsoft Sentinel compared to other SIEMs.

:::image type="content" source="media/migration-arcsight-detection-rules/compare-rule-terminology.png" alt-text="Diagram comparing Microsoft Sentinel rule terminology with other SIEMs." lightbox="media/migration-arcsight-detection-rules/compare-rule-terminology.png":::

## Migrate rules

### Rule 1

This is a rule of type filter (or).

#### Sample detection rule in ArcSight

:::image type="content" source="media/migration-arcsight-detection-rules/rule-1-sample.png" alt-text="Diagram illustrating a sample detection rule." lightbox="media/migration-arcsight-detection-rules/rule-1-sample.png":::

#### Sample KQL query

```kusto
SecurityEvent
| where EventID == 4728
| where SubjectUserName =~ "AutoMatedService"
| where isnotempty(SubjectDomainName)
```
This assumes that the Windows Security Events are collected via MMA/AMA.Hence, we are using SecurityEvent table in Azure Sentinel.

Considerations:
- Avoid case-insensitive operators (=~) when possible for query optimization.
- Use (==) if the value is not case-sensitive.
- Order the filters by starting with the 'where' statement that filter out the most data.

#### Learn more

- [String Operators](/azure/data-explorer/kusto/query/datatypes-string-operators#operators-on-strings)
- Numerical Operators
- ago
- Datetime
- between
- now
- parse
- extract
- parse_json
- parse_csv
- parse_path
- parse_url


