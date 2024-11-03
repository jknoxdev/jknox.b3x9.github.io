---
title: Installing Entra for Powershell
description: Brief command list for setting up the Entra modules for Powershell
date: 2024-11-02
scheduled: 2024-11-02
layout: layouts/post.njk
tags:
  - microsoft
  - powershell
  - entra
---


##### installing entra powershell

```powershell
# add psgallery
Register-PSRepository -Default


# ----- fix module crap -----
## Update Nuget Package and PowerShellGet Module
Install-PackageProvider NuGet -Scope CurrentUser -Force
Install-Module PowerShellGet -Scope CurrentUser -Force -AllowClobber

## Import updated module
Import-Module PowerShellGet -MinimumVersion 2.0 -Force
Import-PackageProvider PowerShellGet -MinimumVersion 2.0 -Force


# now we can install entra module
# make sure to hit tab when typing allowprerelase 
# this will tell you if the globbed package manager 
# has picked up the dependencies
Install-Module -Name Microsoft.Graph.Entra -AllowPrerelease -Repository PSGallery -Force

# pump up the max function count
$MaximumFunctionCount = 32768

# now we can import the module! 
import-module microsoft.graph.entra
```
![[https://imagedelivery.net/h6duaPVMwqSx6OPYA68aOw/c653cc8a-7b1b-469f-eade-bc19b7c86900/public]]


```powershell
# test out the commands:

Connect-Entra -Scopes 'User.Read.All'
Get-Command -Module Microsoft.Graph.Entra

Get-Help Get-EntraUser -Detailed

Connect-Entra -Scopes 'User.Read.All'
Get-EntraUser -UserId
```




##### sources

fixing module crap:
https://learn.microsoft.com/en-us/powershell/entra-powershell/troubleshooting?view=entra-powershell&tabs=v1

background info:
https://learn.microsoft.com/en-us/powershell/entra-powershell/entra-powershell-faqs?view=entra-powershell
https://learn.microsoft.com/en-us/powershell/microsoftgraph/get-started?view=graph-powershell-1.0

https://learn.microsoft.com/en-us/entra/architecture/architecture-icons