---
title: "Usage"
chapter: false
weight: 52
---

## **How to use Mend SCA result?**

Now when you finish scanning your project, you are supposed to see the results of your work.
There are plenty of the [Mend APIs](https://docs.mend.io/bundle/api_sca/page/http_api_v1_3_and_v1_4.html) that you can use to analyze your project, but let's focus first on the basic information:

To get the scan results we need to pull them from the Mend Server, using Mend APIs.
The Mend API is simply an HTTP endpoint that implements JSON web service and handles POST requests. Like the service itself, communication is secured with SSL.

{{% notice note %}}
The header must include: **content-type=application/json** and only POST requests are accepted
{{% /notice %}}

#### **Inventory report**

Let's first look at the inventory of your project. The Inventory report provides a BOM (Bill Of Materials) of all open-source libraries.
Define the URL, content-type, POST HTTP method and run the following request from Postman:

{{% notice info %}}
Both your Product Token and User Key can be found in the confirmation email you received during the [Setting Up Mend User Account](/2_prerequisites/10_setup_mend_account.html) step.
{{% /notice %}}

    {
        "requestType" : "getProductInventoryReport",
        "userKey": "yourUserKey", 
        "productToken" : "yourProductToken",
        "format": "xlsx"
    }

{{% notice note %}}
You can also get this report in json format by specifying "format": "json"
{{% /notice %}}

**Enjoy the result**

![Update build specification](/images/mend-sca/mend-sca-xlsx-results-inventory.png)

#### **Vulnerabilities report**

Now that you have a list of all the libraries and licenses within your project let's look at the security vulnerabilities  

    {
        "requestType" : "getProductVulnerabilityReport",
        "userKey": "yourUserKey", 
        "productToken" : "yourProductToken",
        "format": "xlsx"
    }

{{% notice note %}}
You can also get this report in json format by specifying "format": "json"
{{% /notice %}}

With this report, you can analyze your project, prioritize by severity, number of occurrences, a library that was found vulnerable, and so on

![Update build specification](/images/mend-sca/mend-sca-xlsx-results-vulnerabilities.png)



#### **Risk report**
Want a bird's-eye view of all aspects of your project's open-source libraries with regard to security, quality and compliance? Risk Report is a management-level tool that provides that

    {
        "requestType" : "getProductRiskReport",
        "userKey": "yourUserKey", 
        "productToken" : "yourProductToken"
    }

Analyze the result

![Update build specification](/images/mend-sca/mend-sca-xlsx-results-risk.png)
![Update build specification](/images/mend-sca/mend-sca-xlsx-results-risk-gen-overview.png)

{{% notice tip %}}
To further explore Mend APIs and get more information about the compliance and security of your just scanned project, visit [Mend APIs](https://docs.mend.io/bundle/api_sca/page/http_api_v1_3_and_v1_4.html)
{{% /notice %}}