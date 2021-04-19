---
title: 'How to use expression builder with cloud sync'
description: This article describes how to use the expression builder with cloud sync.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
---

# Expression builder with cloud sync
The expression builder is a new blade in Azure located under cloud sync.  It assits in building complex expressions and allows you to test these expressions before you apply them to your cloud sync environment.

## Use the expression builder
To access the expression builder, use the following steps.

 1. In the Azure portal, select **Azure Active Directory**
 2. Select **Azure AD Connect**.
 3. Select **Manage cloud sync**.
 4. Under **Configuration**, select your configuration.
 5. Under **Manage attributes**, select **Click to edit mappings**.
 6. On the **Edit attribute mappings** blade, click **Add attribute mapping**.
 7. Under **Mapping type** select **Expression**.
 8. Select **Try the expression builder (Preview)**.
 ![Use expression builder](media/how-to-expression-builder/expression-1.png)

## Build an expression
This sections allows you to use the drop-down to select from a list of supported functions.  Then it provides additonal fields for you to fill in, depending on the function selected.  Once you select **Apply expression**, the syntax will appear in the **Expression input** box.

For example, by selecting **Replace** from the drop-down, additonal boxes are provided.  The syntax for the function is displayed in the light blue box.  The boxes that are displayed correspond to the syntax of the function you selected.  Replace works differntly depending on the parameters provided.  For our example we will use:

- When oldValue and replacementValue are provided:
    - Replaces all occurrences of oldValue in the source with replacementValue

For additional information, see [Replace](reference-expressions.md#replace)

The first thing we need to do is select the attribute that is the source for the replace. In our example, we selected the **mail** attribute. 

Next, we fill in the value for oldValue.  This will be **@fabrikam.com**.  Finally, in the box for replacementValue, we will fill in the value **@contoso.com**.

So our expression, basically says, replace the mail attribute on user objects that have a value of @fabrikam.com with the @contoso.com value.  By clicking the **Add expression** button, we can see the syntax in the **Expression input**

 ![Build an expression with Azure](media/how-to-expression-builder/expression-3.png)

>[!NOTE]
>Be sure to place the values in the boxes that would correspond with oldValue and replacementValue based on the syntax that occurs when you have selected Replace.

For additional information on supported expressions see [Writing expressions for attribute mappings in Azure Active Directory](reference-expressions.md)

## Test an expression
In this section, you can test your expressions.  From the drop-down, select the **mail** attribute.  Fill in the value with **@fabrikam.com** and now click **Test expression**.  

You will see the value of **@contoso.com** displayed in the **View expression output** box.

 ![Test your expression](media/how-to-expression-builder/expression-4.png)

## Next steps 

- [Writing expressions for attribute mappings in Azure Active Directory](reference-expressions.md)
- [Cloud sync configuration](how-to-configure.md)
