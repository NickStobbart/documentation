---
title: How to configure the Azure Stack Hub user's Azure CLI environment | Based on Microsoft Docs
description: Learn how to use the cross-platform command-line interface (CLI) to manage and deploy resources on Azure Stack Hub
services: azure-stack
author: Chris Black
reviewer: Daniel Brennand
lastreviewed: 25/03/2020 17:03:00

toc_rootlink: Users
toc_sub1: How To
toc_sub2: Configure Environment
toc_sub3:
toc_sub4:
toc_title: Configure the Azure Stack Hub user's Azure CLI environment
toc_fullpath: Users/How To/azs-how-configure-cli.md
toc_mdlink: azs-how-configure-cli.md
---

# How to configure the Azure Stack Hub user's Azure CLI environment

In this article, we will guide you through the process of installing and using the Azure command-line interface (CLI) to manage Azure Stack Hub. Azure CLI can be used to manage resources such as create virtual machines, deploy Azure Resource Manager templates, etc.

## Microsoft documentation

[Azure CLI command reference for 2018-03-01-hybrid profile](https://docs.microsoft.com/en-us/cli/azure/reference-index?view=azure-cli-2018-03-01-hybrid)

> [!NOTE]
> There is currently no document for the 2019-03-01-hybrid profile.

[Deploy templates with Azure CLI](https://docs.microsoft.com/en-us/azure-stack/user/azure-stack-deploy-template-command-line)

> [!IMPORTANT]
> Azure Stack Hub works with very specific **API Profiles** - you need to use `2019-03-01-hybrid` to interact with it. The biggest caveat is that it only supports a small subset of commands from the `latest profile`.

## Install CLI

# [Local workstation](#tab/tabid-1)

Sign in to your development workstation and install CLI. Azure Stack Hub requires at least version 2.0 of Azure CLI. You can install that by using the steps described in the [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) article. To verify if the installation was successful, open a terminal or a command prompt window and run the following command:

   ```azurecli
      bash-5.0# az --version
      azure-cli                          2.2.0

      command-modules-nspkg              2.0.3
      core                               2.2.0
      nspkg                              3.0.4
      telemetry                          1.0.4

      Python location '/usr/local/bin/python'
      Extensions directory '/root/.azure/cliextensions'

      Python (Linux) 3.6.9 (default, Nov 15 2019, 03:58:01)
      [GCC 8.3.0]

      Legal docs and information: aka.ms/AzureCliLegal



      Your CLI is up-to-date.

      Please let us know how we are doing: https://aka.ms/clihats
   ```

You should see the version of Azure CLI and other dependent libraries that are installed on your computer.

# [Docker container](#tab/tabid-2)

If you have [Docker](https://www.docker.com/) installed on your workstation, Microsoft provides an Azure CLI container that you can use instead of installing dependencies locally.

To start the Azure CLI docker container, run the following command:

   ```azurecli
      docker run -it --rm mcr.microsoft.com/azure-cli
   ```

> [!NOTE]
> The above command will automatically pull the container image from Microsoft's container registry.
>
> If you experience issues with pulling the image, run the following command:
>
> ```azurecli
> docker pull mcr.microsoft.com/azure-cli
> ```

> [!CAUTION]
> If you are running Docker Desktop on Windows and receive the following error:
> 
> ```cmd
> C:\Program Files\Docker\Docker\Resources\bin\docker.exe: image operating system "linux" cannot be used on this platform.
> ```
> Ensure Docker Desktop on Windows is set to **Linux containers**.

This command will execute an interactive terminal session inside the container and remove it on exit.

You will now be in the container's interactive terminal session and see the following prompt: `bash-5.0#`.

You can run `az --version` to verify the Azure CLI is working correctly:

```azurecli
   bash-5.0# az --version
   azure-cli                          2.2.0

   command-modules-nspkg              2.0.3
   core                               2.2.0
   nspkg                              3.0.4
   telemetry                          1.0.4

   Python location '/usr/local/bin/python'
   Extensions directory '/root/.azure/cliextensions'

   Python (Linux) 3.6.9 (default, Nov 15 2019, 03:58:01)
   [GCC 8.3.0]

   Legal docs and information: aka.ms/AzureCliLegal



   Your CLI is up-to-date.

   Please let us know how we are doing: https://aka.ms/clihats
```

***

## Declare variables

Enter details below to provide values for the variables in the scripts in this article:

| Variable  | Variable description                                      | Input            |
|-----------------|-----------------------------------------------------------|------------------|
| Azure Stack Hub DNS Suffix | The DNS suffix for Azure Stack Hub (&lt;region&gt;.&lt;External Domain Name&gt;)  | <form oninput="result.value=dnssuffix.value;result2.value=dnssuffix.value;result3.value=dnssuffix.value;result4.value=dnssuffix.value;result5.value=dnssuffix.value;result6.value=dnssuffix.value;result7.value=dnssuffix.value;result8.value=dnssuffix.value;result9.value=dnssuffix.value" id="dnssuffix" style="display: inline;"><input type="text" id="dnssuffix" name="dnssuffix" style="display: inline;" placeholder="frn00006.azure.ukcloud.com"/></form> |
| Username               | Your AAD username                                                             | <form oninput="result.value=username.value;result2.value=username.value" id="username" style="display: inline;"><input type="text" id="username" name="username" style="display: inline;" placeholder="user@contoso.onmicrosoft.com"/></form> |
| Password               | Your AAD password                                                             | <form oninput="result.value=password.value;result2.value=password.value" id="password" style="display: inline;"><input type="text" id="password" name="password" style="display: inline;" placeholder="Password123!"/></form> |

## Connect to Azure Stack Hub

Use the following steps to connect to Azure Stack Hub:

1. Register your Azure Stack Hub environment by running the `az cloud register` command.

   To register the *user* environment, use:

      <pre><code class="lang-azurecli hljs">az cloud register \
        -n "AzureStackUser" \
        --endpoint-resource-manager "https://management.<output form="dnssuffix" name="result" style="display: inline;">frn00006.azure.ukcloud.com</output>" \
        --suffix-storage-endpoint "<output form="dnssuffix" name="result2" style="display: inline;">frn00006.azure.ukcloud.com</output>" \
        --suffix-keyvault-dns ".vault.<output form="dnssuffix" name="result3" style="display: inline;">frn00006.azure.ukcloud.com</output>" \
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/"</code></pre>

   To register the *user* environment - One Liner:

      <pre><code class="lang-azurecli hljs">az cloud register -n AzureStackUser --endpoint-resource-manager "https://management.<output form="dnssuffix" name="result4" style="display: inline;">frn00006.azure.ukcloud.com</output>" --suffix-storage-endpoint "<output form="dnssuffix" name="result5" style="display: inline;">frn00006.azure.ukcloud.com</output>" --suffix-keyvault-dns ".vault.<output form="dnssuffix" name="result6" style="display: inline;">frn00006.azure.ukcloud.com</output>" --endpoint-active-directory-graph-resource-id "https://graph.windows.net/"</code></pre>

2. Set the active environment by using the following commands.

   For the *user* environment, use:

      ```azurecli
      az cloud set -n AzureStackUser
      ```

3. Update your environment configuration to use the Azure Stack Hub specific API version profile. To update the configuration, run the following command:

   ```azurecli
   az cloud update --profile 2019-03-01-hybrid
   ```

4. Sign in to your Azure Stack Hub environment by using the `az login` command. You can sign in to the Azure Stack Hub environment either as a user or as a [service principal](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-objects).

   - Sign in as a *user*: You can either specify the username and password directly within the `az login` command or authenticate by using a browser. You have to do the latter if your account has multi-factor authentication enabled.
   - Example of *username*: Active directory global administrator or user account i.e. username@&lt;aadtenant&gt;.onmicrosoft.com or username@domain.com

      <pre><code class="lang-azurecli hljs">az login -u "<output form="username" name="result" style="display: inline;">user@contoso.onmicrosoft.com</output>" -p '<output form="password" name="result" style="display: inline;">Password123!</output>'</code></pre>

      > [!NOTE]
      > If your user account has multi-factor authentication enabled, you can use the `az login` command without providing the `-u` parameter. Running the command gives you a URL and a code that you must use to authenticate.

      > [!CAUTION]
      > If the user is not present in the domain that you are attempting to login to, you must specify the `--tenant` parameter.
      > For example, if your guest user is `user@contoso.onmicrosoft.com` and the domain is `fabrikam@onmicrosoft.com` the command will be as follows:
      >
      > ```azurecli
      > az login --tenant fabrikam@onmicrosoft.com
      > To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code xxxxxxxxx to authenticate.
      > ```
      >
      > At the user authentication page, sign in with your guest user `user@contoso.onmicrosoft.com`.

   - Sign in as a *service principal*: Before you sign in, create a service principal through [the public Azure portal](azs-how-create-spn-portal.md), [PowerShell](azs-how-create-spn-powershell.md) or [CLI](azs-how-create-spn-cli.md) and assign it a role. Now, sign in by using the following command:

      ```azurecli
      az login \
        --tenant <Azure Active Directory Tenant name. For example: contoso.onmicrosoft.com> \
        --service-principal \
        -u <Application Id of the Service Principal> \
        -p <Key generated for the Service Principal>
      ```

5. Verify that your environment is set correctly and that AzureStackUser is the active cloud.

      ```azurecli
      bash-5.0# az cloud list --output table
      IsActive    Name               Profile
      ----------  -----------------  -----------------
      False       AzureCloud         latest
      False       AzureChinaCloud    latest
      False       AzureUSGovernment  latest
      False       AzureGermanCloud   latest
      True        AzureStackUser     2019-03-01-hybrid
      ```

      > [!NOTE]
      > The final line shows that the user profile has been updated and is active.

6. To list command subgroups run:

      ```azurecli
      az --help
      ```

7. To list commands for specific subgroup run:

      ```azurecli
      az <subgroupname> --help
      ```

## Connect to Azure Stack Hub - Quick Reference Guide

<pre><code class="lang-azurecli hljs"># Create your environment
az cloud register -n AzureStackUser --endpoint-resource-manager "https://management.<output form="dnssuffix" name="result7" style="display: inline;">frn00006.azure.ukcloud.com</output>" --suffix-storage-endpoint "<output form="dnssuffix" name="result8" style="display: inline;">frn00006.azure.ukcloud.com</output>" --suffix-keyvault-dns ".vault.<output form="dnssuffix" name="result9" style="display: inline;">frn00006.azure.ukcloud.com</output>" --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" --profile 2019-03-01-hybrid

# Set your environment
az cloud set -n AzureStackUser

# Log in to Azure Stack Hub with user credentials
az login -u "<output form="username" name="result2" style="display: inline;">user@contoso.onmicrosoft.com</output>" -p '<output form="password" name="result2" style="display: inline;">Password123!</output>'</code></pre>

## Test the connectivity

Now that we've got everything setup, let's use CLI to create resources within Azure Stack Hub. For example, you can create a resource group for an application and add a virtual machine. Use the following command to create a resource group named "MyResourceGroup":

```azurecli
az group create -n MyResourceGroup -l frn00006
```

If the resource group is created successfully, the previous command outputs the following properties of the newly created resource:

![Resource group create output](https://docs.microsoft.com/en-us/azure/azure-stack/user/media/azure-stack-connect-cli/image1.png)

## Known issues

There are some known issues that you must be aware of when using CLI in Azure Stack Hub:

- The CLI interactive mode i.e the `az interactive` command is not yet supported in Azure Stack Hub.

- To get the list of virtual machine images available in Azure Stack Hub, use the `az vm image list --all` command instead of the `az vm image list` command. Specifying the `--all` option makes sure that response returns only the images that are available in your Azure Stack Hub environment.

- Virtual machine image aliases that are available in Azure may not be applicable to Azure Stack Hub. When using virtual machine images, you must use the entire URN parameter (Canonical:UbuntuServer:14.04.3-LTS:1.0.0) instead of the image alias. This URN must match the image specifications as derived from the `az vm image list -all` command.

## Feedback

If you find a problem with this article, click **Improve this Doc** to make the change yourself or raise an [issue](https://github.com/UKCloud/documentation/issues) in GitHub. If you have an idea for how we could improve any of our services, send an email to <feedback@ukcloud.com>.
