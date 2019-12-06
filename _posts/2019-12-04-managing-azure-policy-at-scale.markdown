---
layout: post
title: Managing Azure Policy at scale
date: 2019-12-04 22:32:20 +1000
description: Azure Policy Management through Azure DevOps # Add post description (optional)
img: azurepolicy.png # Add image post (optional)
tags: [Azure Policy, Governance, Azure DevOps]
author: # Add name author (optional)
---

Azure Policy is a fantastic native tool to provide governance to your cloud environment. With the recent annoucement of Azure Arc we can now extend the reach of Azure Policy to the edge, on-premises, and to other clouds all under the one service providing a central visibility and control point. Arc is only in preview today and limited to virtual machine and K8s clusters but I can see a possible future that extends this further. You can check out further information on Arc [here][azure-arc].

Azure Policy is here to stay and only growing in its application and usefulness. If like me, you operate in a highly regulated industry and you may have a requirement to manage Azure Policy at large scale across many hundreds of subscriptions, this management has to be formed of a controlled and where possible automated process. The following series of posts will provide some guidance on how I have achieved this to suit my requirments which may be useful for others to adopt all of parts of as well.

The solution is comprised of the following components:

- Defining your Management Group structure
- Understand what policy controls are required in the enviroment

## Optional Enhancement Ideas

**Deploy all policies assignments using custom policy definitions** In doing this we can set the policy ID to a value we know is specific to us and not a built-in policy. This also allows us to name the policy definitions to a name format meaningful to our needs. We have also assigned each policy a unique ID which prepends the name in the `DisplayName`. This allows us to easily search and visually order when we have hundreds of policies defined.

**Set the description of a policy to something more meaningful** How about a link to a wiki page where you can provide further information and help on the policy. This may include specific security outcomes, correlation to your security framework controls the policy meets and guidance to your audience on what they need to do to meet compliance for the policy.

## The Policy Definition process

Our first task is to define and process the 


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