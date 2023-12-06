---
title: Enable CAPTCHA in Azure Active Directory B2C  
description: How to enable CAPTCHA for user flows and custom policies in Azure Active Directory B2C.
author: kengaderdus
manager: mwongerapk
ms.service: active-directory
ms.topic: how-to
ms.date: 12/11/2023
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type

#Customer intent: As a developer, I want to learn how to enable CAPTCHA in consumer-facing applications secured by Azure Active Directory B2C.

---

# Enable CAPTCHA in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) enables you to enable CAPTCHA prevent automated attacks on your consumer-facing applications. Azure AD B2C’s CAPTCHA supports both audio and visual captcha challenges. You can enable this security feature in both sign-up and sign-in flows of your local accounts. CAPTCHA isn't applicable for social identity providers' sign-in.   

> [!NOTE]
> This feature is in public preview

## Prerequisites

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)] 

## Enable CAPTCHA 

::: zone pivot="b2c-user-flow"

1. Sign in to the [Azure portal](https://portal.azure.com).

1. If you have access to multiple tenants, select the **Settings** icon in the top menu to switch to your Azure AD B2C tenant from the **Directories + subscriptions** menu.

1. In the left menu, select **Azure AD B2C**. Or, select **All services** and search for and select **Azure AD B2C**.

1. Select **User flows**.

1. Select the user flow for which you want to enable CAPTCHA. For example, *B2C_1_signinsignup*.

1. Select **Properties**.

1. Under **CAPTCHA (Preview)**, select the flow for which to enable CAPTCHA for, such as **Enable CAPTCHA - Sign Up**. 

1. Select **Save**.

## Test the user flow

Use the steps in [Test the user flow](tutorial-create-user-flows.md?pivots=b2c-user-flow#test-the-user-flow-1) to test and confirm that CAPTCHA is enabled for your chosen flow. You should be prompted to enter the characters you see or hear depending on the CAPTCHA type, visual or audio, you choose.

::: zone-end


::: zone pivot="b2c-custom-policy"

To enable CAPTCHA in your custom policy, you need to update your existing custom policy files. If you don't have any existing custom policy files, [Download the .zip file](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) or clone the repository from `https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack`, and then update the XML files in */Display Controls Starterpack/LocalAccounts/* folder.

### Declare claims

You need additional claims to enable you enable CAPTCHA:

1. In VS Code, open the *TrustFrameworkBase.XML* file.

1. In the `ClaimsSchema` section, declare claims by using the following code:

    ```xml
     <!--<ClaimsSchema>-->
      ...
      <ClaimType Id="inputSolution">
        <DataType>string</DataType>
      </ClaimType>

      <ClaimType Id="solved">
        <DataType>boolean</DataType>
      </ClaimType>

      <ClaimType Id="reason">
        <DataType>string</DataType>
      </ClaimType>

      <ClaimType Id="azureregion">
        <DataType>string</DataType>
      </ClaimType>

      <ClaimType Id="challengeId">
        <DisplayName>The ID of the generated captcha</DisplayName>
        <DataType>string</DataType>
        <UserHelpText>Captcha challenge identifier</UserHelpText>
        <UserInputType>Paragraph</UserInputType>
      </ClaimType>

      <ClaimType Id="challengeType">
        <DisplayName>Type of captcha (visual / audio)</DisplayName>
        <DataType>string</DataType>
        <UserHelpText>Captcha challenge type</UserHelpText>
        <UserInputType>Paragraph</UserInputType>
      </ClaimType>

      <ClaimType Id="challengeString">
        <DisplayName>Captcha challenge code</DisplayName>
        <DataType>string</DataType>
        <UserHelpText>Captcha challenge code</UserHelpText>
        <UserInputType>Paragraph</UserInputType>
      </ClaimType>

      <ClaimType Id="captchaEntered">
        <DisplayName>Captcha entered by the user</DisplayName>
        <DataType>string</DataType>
        <UserHelpText>Enter the characters you see</UserHelpText>
        <UserInputType>TextBox</UserInputType>
      </ClaimType>

      <ClaimType Id="isCaptchaSolved">
        <DisplayName>Flag indicating that the captcha was successfully solved</DisplayName>
        <DataType>boolean</DataType>
      </ClaimType>
      ...
     <!--<ClaimsSchema>-->
    ``` 

### Configure a display control

To enable CAPTCHA for your custom policy, you use a CAPTCHA Display Control. The display control renders the CAPTCHA image.

In the *TrustFrameworkBase.XML* file, locate the `DisplayControls` element, then add the following display control as a child element. If you don't already have `DisplayControls` element, add one.

```xml
<!--<DisplayControls>-->
...    
<DisplayControl Id="captchaControlChallengeCode" UserInterfaceControlType="CaptchaControl" DisplayName="Help us beat the bots">    
    <InputClaims>
    <InputClaim ClaimTypeReferenceId="challengeType" />
    <InputClaim ClaimTypeReferenceId="challengeId" />
    </InputClaims>

    <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="challengeType" ControlClaimType="ChallengeType" />
    <DisplayClaim ClaimTypeReferenceId="challengeId" ControlClaimType="ChallengeId" />
    <DisplayClaim ClaimTypeReferenceId="challengeString" ControlClaimType="ChallengeString" />
    <DisplayClaim ClaimTypeReferenceId="captchaEntered" ControlClaimType="CaptchaEntered" />
    </DisplayClaims>

    <Actions>
    <Action Id="GetChallenge">
        <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile
            TechnicalProfileReferenceId="HIP-GetChallenge" />
        </ValidationClaimsExchange>
    </Action>

    <Action Id="VerifyChallenge">
        <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile
            TechnicalProfileReferenceId="HIP-VerifyChallenge" />
        </ValidationClaimsExchange>
    </Action>
    </Actions>
</DisplayControl> 
...
<!--</DisplayControls>-->
```

### Configure a CaptchaProvider technical profile 

Azure AD B2C provides a technical profile that verifies the CAPTCHA challenge. This technical profile can generate a CAPTCHA image or verifies the CAPTCHA string depending on how you configure it.

In the *TrustFrameworkBase.XML* file, locate the `ClaimsProviders` element and add the claims provider by using the following code:

```xml
<!--<ClaimsProvider>-->
...
<ClaimsProvider>

    <DisplayName>HIPChallenge</DisplayName>

    <TechnicalProfiles>

    <TechnicalProfile Id="HIP-GetChallenge">
        <DisplayName>GetChallenge</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.CaptchaProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="Operation">GetChallenge</Item>
        <Item Key="Brand">HIP</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="challengeType" />
        </InputClaims>
        <DisplayClaims>
        <DisplayClaim ClaimTypeReferenceId="challengeString" />
        </DisplayClaims>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="challengeId" />
        <OutputClaim ClaimTypeReferenceId="challengeString" PartnerClaimType="ChallengeString" />
        <OutputClaim ClaimTypeReferenceId="azureregion" />
        </OutputClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="HIP-VerifyChallenge">
        <DisplayName>Verify Code</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.CaptchaProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
        <Item Key="Brand">HIP</Item>
        <Item Key="Operation">VerifyChallenge</Item>
        </Metadata>
        <InputClaims>
        <InputClaim ClaimTypeReferenceId="challengeType" DefaultValue="Visual" />
        <InputClaim ClaimTypeReferenceId="challengeId" />
        <InputClaim ClaimTypeReferenceId="captchaEntered" PartnerClaimType="inputSolution" Required="true" />
        <InputClaim ClaimTypeReferenceId="azureregion" />
        </InputClaims>
        <DisplayClaims>
        <DisplayClaim ClaimTypeReferenceId="captchaEntered" />
        </DisplayClaims>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="challengeId" />
        <OutputClaim ClaimTypeReferenceId="isCaptchaSolved" PartnerClaimType="solved" />
        <OutputClaim ClaimTypeReferenceId="reason" PartnerClaimType="reason" />
        </OutputClaims>
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>    
...
<!--<ClaimsProviders>-->
```

### Update content definition's page layouts

For the various page layouts, use the following page layout versions:

|Page layout |Page layout version range |
|---------|---------|
| Selfasserted  | >=2.1.29 |
| Unifiedssp  | >=2.1.17 |
|  Multifactor  |    >=1.2.15  |

**Example:**

In the *TrustFrameworkBase.XML* file, under the `ContentDefinitions` element, locate a content definition with *Id="api.localaccountsignup"*, then updates its *DataUri* as shown in the following code:

```xml
<!---<ContentDefinitions>-->
...
<ContentDefinition Id="api.localaccountsignup">
    ...
    <!--Update this DataUri-->
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.27</DataUri>
    ...
</ContentDefinition>
...
<!---</ContentDefinitions>-->
```
We've specified the selfasserted page layout version as *2.1.27*.

Once you configure your technical profiles and display controls, you can specify the flow for which you want to enable CAPTCHA.

### Enable CAPTCHA for sign-up or sign-in flow  
 
To enable CAPTCHA for your sign-up or sign-in flow, use the following steps:

1. Inspect your sign-up sign-in user journey, such as *SignUpOrSignIn*, to identify the self asserted technical profile that's responsible for your sign-up or sign-in flow. 

1. In the technical profile, such as *LocalAccountSignUpWithLogonEmail*, add a metadata key and a display claim entries as shown in the following code:

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
    ...
  <Metadata>
    ...
    <!--Add this metadata entry. Value set to true-->
    <Item Key="setting.enableCaptchaChallenge">true</Item>
    ...
  </Metadata>
    ...
  <DisplayClaims>
    ...
    <!--Add this display claim-->
    <DisplayClaim DisplayControlReferenceId="captchaControlChallengeCode" /> 
    ...
  </DisplayClaims>
    ...
</TechnicalProfile>
```
The display claim entry references the display control that you configured earlier. 

### Enable CAPTCHA in MFA flow 

To enable CAPTCHA in MFA flow, you need to make an update in two technical profiles, that is, in the self-asserted technical profile, and in the [phone factor technical profile](phone-factor-technical-profile.md): 

1. Inspect your sign-up sign-in user journey, such as *SignUpOrSignIn*, to identify the self-asserted technical profile and phone factor technical profiles that are responsible for your sign-up or sign-in flow. 

1. In both of the technical profiles, add a metadata key and a display claim entries as shown in the following code:

```xml
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
    ...
  <Metadata>
    ...
    <!--Add this metadata entry. Value set to true-->
    <Item Key="setting.enableCaptchaChallenge">true</Item>
    ...
  </Metadata>
    ...
  <DisplayClaims>
    ...
    <!--Add this display claim-->
    <DisplayClaim DisplayControlReferenceId="captchaControlChallengeCode" /> 
    ...
  </DisplayClaims>
    ...
</TechnicalProfile>
```

## Test the custom policy

Use the steps in [Test the custom policy](tutorial-create-user-flows.md?pivots=b2c-custom-policy#test-the-custom-policy) to test and confirm that CAPTCHA is enabled for your chosen flow. You should be prompted to enter the characters you see or hear depending on the CAPTCHA type, visual or audio, you choose.
::: zone-end