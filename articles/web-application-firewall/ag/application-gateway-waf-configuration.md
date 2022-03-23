---
title: Web application firewall exclusion lists in Azure Application Gateway - Azure portal
description: This article provides information on Web Application Firewall exclusion lists configuration in Application Gateway with the Azure portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/15/2022
ms.author: victorh
ms.topic: conceptual 
ms.custom: devx-track-azurepowershell
---

# Web Application Firewall exclusion lists

The Azure Application Gateway Web Application Firewall (WAF) provides protection for web applications. This article describes the configuration for WAF exclusion lists. These settings are located in the WAF Policy associated to your Application Gateway. To learn more about WAF Policies, see [Azure Web Application Firewall on Azure Application Gateway](ag-overview.md) and [Create Web Application Firewall policies for Application Gateway](create-waf-policy-ag.md)

Sometimes Web Application Firewall (WAF) might block a request that you want to allow for your application. WAF exclusion lists allow you to omit certain request attributes from a WAF evaluation. The rest of the request is evaluated as normal.

For example, Active Directory inserts tokens that are used for authentication. When used in a request header, these tokens can contain special characters that may trigger a false positive from the WAF rules. By adding the header to an exclusion list, you can configure WAF to ignore the header, but WAF still evaluates the rest of the request.

You can configure exclusions to apply when specific WAF rules are evaluated, or to apply globally to the evaluation of all WAF rules. Exclusion rules apply to your whole web application.

<!-- TODO
## Configure excluson lists

To set exclusion lists in the Azure portal, configure **Exclusions** in the WAF policy resource's **Policy settings** page:

:::image type="content" source="../media/application-gateway-waf-configuration/waf-policy-exclusions.png" alt-text="Screenshot of the Azure portal that shows the exclusions configuration for the W A F policy.":::
-->

## Identify request attributes to exclude

When you configure a WAF exclusion, you must specify the attributes of the request that should be excluded from the WAF evaluation. The values of the specified attributes aren't evaluated against WAF rules, but their names still are.

You can configure a WAF exclusion for the following request attributes:

* Request headers
* Request cookies
* Form fields
* JSON entities
* URL query string arguments

You can specify an exact request header, body, cookie, or query string attribute match. Or, you can optionally specify partial matches. Use the following operators to configure the exclusion:

- **Equals**:  This operator is used for an exact match. As an example, for selecting a header named **bearerToken**, use the equals operator with the selector set as **bearerToken**.
- **Starts with**: This operator matches all fields that start with the specified selector value.
- **Ends with**:  This operator matches all request fields that end with the specified selector value.
- **Contains**: This operator matches all request fields that contain the specified selector value.
- **Equals any**: This operator matches all request fields. * will be the selector value.

In all cases matching is case insensitive. Regular expression aren't allowed as selectors.

> [!NOTE]
> For more information and troubleshooting help, see [WAF troubleshooting](web-application-firewall-troubleshoot.md).

## Exclusion scopes

Exclusions can be configured to apply to a specific set of WAF rules, or globally across all rules.

> [!TIP]
> It's a good practice to make exclusions as narrow and specific as possible, to avoid accidentally leaving room for attackers to exploit your system. When you need to add an exclusion rule, use per-rule exclusions wherever possible.

### Per-rule exclusions

You can configure an exclusion for a specific rule, group of rules, or rule set. You must specify the rule or rules that the exclusion applies to. You also need to specify the request attribute that should be excluded from the WAF evaluation.

Per-rule exclusions are available when you use the OWASP (CRS) Ruleset version 3.2 or later.

#### Example

In this example, you want to exclude the `User-Agent` request header. The `User-Agent` header contains a characteristic string that allows the network protocol peers to identify the application type, operating system, software vendor, or software version of the requesting software user agent. For more information, see [User-Agent](https://developer.mozilla.org/docs/Web/HTTP/Headers/User-Agent).

There can be any number of reasons to disable evaluating this header. There could be a string that the WAF sees and assumes it’s malicious. For example, the classic SQL attack “x=x” in a string. In some cases, this can be legitimate traffic. So you might need to exclude this header from WAF evaluation.

You can use the folllowing code to exclude the `User-Agent` header from evaluation by all of the SQL injection rules:

# [Azure portal](#tab/portal)

:::image type="content" source="../media/application-gateway-waf-configuration/waf-policy-exclusions-rule-edit.png" alt-text="Screenshot of the Azure portal that shows the per-rule exclusion configuration for the W A F policy.":::

# [Azure PowerShell](#tab/powershell)

```azurepowershell
$ruleGroupEntry = New-AzApplicationGatewayFirewallPolicyExclusionManagedRuleGroup `
  -RuleGroupName 'REQUEST-942-APPLICATION-ATTACK-SQLI'

$exclusionManagedRuleSet = New-AzApplicationGatewayFirewallPolicyExclusionManagedRuleSet `
  -RuleSetType 'OWASP' `
  -RuleSetVersion '3.2' `
  -RuleGroup $ruleGroupEntry

$exclusionEntry = New-AzApplicationGatewayFirewallPolicyExclusion `
  -MatchVariable "RequestHeaderNames" `
  -SelectorMatchOperator 'Equals' `
  -Selector 'User-Agent' `
  -ExclusionManagedRuleSet $exclusionManagedRuleSet

$wafPolicy = Get-AzApplicationGatewayFirewallPolicy `
  -Name $wafPolicyName `
  -ResourceGroupName $resourceGroupName
$wafPolicy.ManagedRules[0].Exclusions.Add($exclusionEntry)
$wafPolicy | Set-AzApplicationGatewayFirewallPolicy
```

# [Azure CLI](#tab/cli)

<!-- TODO this doesn't seem to work -->
```azurecli
az network application-gateway waf-policy managed-rule exclusion rule-set add \
  --resource-group $resourceGroupName \
  --policy-name $wafPolicyName \
  --type OWASP \
  --version 3.2 \
  --group-name REQUEST-942-APPLICATION-ATTACK-SQLI \
  --match-variable RequestHeaderNames \
  --selector-match-operator Equals \
  --selector User-Agent
```

# [Bicep](#tab/bicep)

```bicep
resource wafPolicy 'Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies@2021-05-01' = {
  name: wafPolicyName
  location: location
  properties: {
    managedRules: {
      managedRuleSets: [
        {
          ruleSetType: 'OWASP'
          ruleSetVersion: '3.2'
        }
      ]
      exclusions: [
        {
          matchVariable: 'RequestHeaderNames'
          selectorMatchOperator: 'Equals'
          selector: 'User-Agent'
          exclusionManagedRuleSets: [
            {
              ruleSetType: 'OWASP'
              ruleSetVersion: '3.2'
              ruleGroups: [
                {
                  ruleGroupName: 'REQUEST-942-APPLICATION-ATTACK-SQLI'
                }
              ]
            }
          ]
        }
      ]
    }
  }
}
```

# [ARM template](#tab/armtemplate)

```json
{
  "type": "Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies",
  "apiVersion": "2021-05-01",
  "name": "[parameters('wafPolicyName')]",
  "location": "[parameters('location')]",
  "properties": {
    "managedRules": {
      "managedRuleSets": [
        {
          "ruleSetType": "OWASP",
          "ruleSetVersion": "3.2"
        }
      ],
      "exclusions": [
        {
          "matchVariable": "RequestHeaderNames",
          "selectorMatchOperator": "Equals",
          "selector": "User-Agent",
          "exclusionManagedRuleSets": [
            {
              "ruleSetType": "OWASP",
              "ruleSetVersion": "3.2",
              "ruleGroups": [
                {
                  "ruleGroupName": "REQUEST-942-APPLICATION-ATTACK-SQLI"
                }
              ]
            }
          ]
        }
      ]
    }
  }
}
```

---

### Global exclusions

You can configure an exclusion to apply across all WAF rules.

#### Example

This example excludes the value in the *user* parameter that is passed in the request via the URL. For example, say it’s common in your environment for the `user` query string argument to contain a string that the WAF views as malicious content, so it blocks it. You can exclude the `user` query string argument so that the WAF doesn't evaluate the field's value.

The following example shows how you can exclude the `user` query string argument from evaluation:

# [Azure portal](#tab/portal)

:::image type="content" source="../media/application-gateway-waf-configuration/waf-policy-exclusions-global-edit.png" alt-text="Screenshot of the Azure portal that shows the global exclusion configuration for the W A F policy.":::

# [Azure PowerShell](#tab/powershell)

```azurepowershell
$exclusion = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable 'RequestArgNames' `
   -SelectorMatchOperator 'StartsWith' `
   -Selector 'user'
```

# [Azure CLI](#tab/cli)

```azurecli
az network application-gateway waf-policy managed-rule exclusion add \
  --resource-group $resourceGroupName \
  --policy-name $wafPolicyName \
  --match-variable 'RequestArgNames' \
  --selector-match-operator 'StartsWith' \
  --selector 'user'
```

# [Bicep](#tab/bicep)

```bicep
resource wafPolicy 'Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies@2021-05-01' = {
  name: wafPolicyName
  location: location
  properties: {
    managedRules: {
      managedRuleSets: [
        {
          ruleSetType: 'OWASP'
          ruleSetVersion: '3.2'
        }
      ]
      exclusions: [
        {
          matchVariable: 'RequestArgNames'
          selectorMatchOperator: 'StartsWith'
          selector: 'user'
        }
      ]
    }
  }
}
```

# [ARM template](#tab/armtemplate)

```json
{
  "type": "Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies",
  "apiVersion": "2021-05-01",
  "name": "[parameters('wafPolicyName')]",
  "location": "[parameters('location')]",
  "properties": {
    "managedRules": {
      "managedRuleSets": [
        {
          "ruleSetType": "OWASP",
          "ruleSetVersion": "3.2"
        }
      ],
      "exclusions": [
        {
          "matchVariable": "RequestArgNames",
          "selectorMatchOperator": "StartsWith",
          "selector": "user"
        }
      ]
    }
  }
}
```

---

If the URL `http://www.contoso.com/?user%281%29=fdafdasfda` is scanned by the WAF, it won't evaluate the string **fdafdasfda**, but it will still evaluate the parameter name **user%281%29**.

## Next steps

After you configure your WAF settings, you can learn how to view your WAF logs. For more information, see [Application Gateway diagnostics](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).
