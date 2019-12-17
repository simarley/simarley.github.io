---
layout: post
title: Managing Azure Policy at scale - part 1
date: 2019-12-04 22:32:20 +1000
description: Azure Policy Management through Azure DevOps # Add post description (optional)
img: azurepolicy.png # Add image post (optional)
tags: [Azure Policy, Governance, Azure DevOps]
author: # Add name author (optional)
---

Azure Policy is a fantastic native tool to provide governance to your cloud environment. With the recent annoucement of Azure Arc we can now extend the reach of Azure Policy to the edge, on-premises, and to other clouds all under the one service providing a central visibility and control point. Arc is only in preview today and limited to virtual machine and K8s clusters but I can see a possible future that extends this further. You can check out further information on Arc [here][azure-arc].

If you have a requirement to manage Azure Policy at scale or if you just want a method of providing some structure to how you management policy, the solution has to managed through a controlled and where possible automated process. The following series of posts will provide some guidance on how I have achieved this to suit my requirments, which may be useful for others to adopt all of parts of.

The solution is comprised of the following components:

- Defining your Management Group structure
- Understand what policy controls are required in the enviroment and match these to possible policy definitions you can create

---


## Optional Enhancements

**Deploy all policies assignments using custom policy definitions** In doing this we can set the policy's ID to a custom value we know is specific to us and not a built-in policy. This also allows us to name the policy definitions to a name format more meaningful to our needs. We can also prepend the unique policy ID in the `DisplayName`. This allows us to easily search and visually order when we have hundreds of policies defined.

**Set the description of a policy to something more meaningful** The description field is limited in characters and not easily read. How about setting this to a URL to a wiki page where we can provide further information on the policy. This could include specific security outcomes details and correlation to your security framework controls the policy meets. Also include guidance to your audience on what they need to do to meet compliance for the particular policy.

## The Policy Definition process

Our first task is to define and process the define and deploy the policies. We need a PowerShell script to define the policy definitions to our Management Group(s). I'm a strong advocate of having the policy defintions defined at the **Tenant Root Group**. This means we only need to deploy and manage one set of definitions and from here they can be assigned to any level in our Management Group structure.

`deploy-policyDef.ps1`




{% highlight json %}
{
    "properties": {
        "description": "https://my-internal-wiki-site.com/114.aspx",
        "displayName": "114-Use of classic virtual machines",
        "metadata": {
            "category": "Compute"
        },
        "mode": "All",
        "parameters": {
            "effect": {
                "allowedValues": [
                    "Audit",
                    "Deny"
                ],
                "type": "String"
            }
        },
        "policyRule": {
            "if": {
                "equals": "Microsoft.classicCompute/virtualMachines",
                "field": "type"
            },
            "then": {
                "effect": "[parameters('effect')]"
            }
        },
        "policyType": "Custom"
    }
}
{% endhighlight %}


[azure-arc]: https://azure.microsoft.com/en-us/services/azure-arc/?&OCID=AID2000713_SEM_W9PSpAAACgPc68Fc:20191205114311:s&ef_id=W9PSpAAACgPc68Fc:20191205114311:s