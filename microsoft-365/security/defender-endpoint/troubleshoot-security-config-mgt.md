---
title: Troubleshoot onboarding issues related to Security Management for Microsoft Defender for Endpoint
description: Troubleshoot issues that might arise during the onboarding of devices using Security Management for Microsoft Defender for Endpoint.
keywords: troubleshoot onboarding, onboarding issues, event viewer, data collection and preview builds, sensor data and diagnostics
ms.prod: m365-security
ms.mktglfcycl: deploy
ms.sitesec: library
ms.pagetype: security
ms.author: macapara
author: mjcaparas
ms.localizationpriority: medium
manager: dansimp
audience: ITPro
ms.collection: M365-security-compliance
ms.topic: troubleshooting
ms.technology: mde
---

# Troubleshoot onboarding issues related to Security Management for Microsoft Defender for Endpoint 

[!INCLUDE [Microsoft 365 Defender rebranding](../../includes/microsoft-defender.md)]


**Applies to:**

- [Manage Microsoft Defender for Endpoint on devices with Microsoft Endpoint Manager](/mem/intune/protect/mde-security-integration)
- [Microsoft Defender for Endpoint](https://go.microsoft.com/fwlink/?linkid=2154037)
- [Microsoft 365 Defender](https://go.microsoft.com/fwlink/?linkid=2118804)

Security Management for Microsoft Defender for Endpoint is a capability for devices that aren’t managed by a Microsoft Endpoint Manager, either Microsoft Intune or Microsoft Endpoint Configuration Manager, to receive security configurations for Microsoft Defender directly from Endpoint Manager.
For more information on Security Management for Microsoft Defender for Endpoint, see [Manage Microsoft Defender for Endpoint on devices with Microsoft Endpoint Manager](/mem/intune/protect/mde-security-integration).

For Security Management for Microsoft Defender for Endpoint onboarding instructions, see [Microsoft Defender for Endpoint Security Configuration Management](security-config-management.md)

This end-to-end onboarding is designed to be frictionless and doesn't require user input. However, if you encounter issues during onboarding, you can view and troubleshoot errors within the Microsoft Defender for Endpoint platform.


>[!NOTE]
> If you are having issues with the onboarding flow for new devices, review the [Microsoft Defender for Endpoint prerequisites](/mem/intune/protect/mde-security-integration#prerequisites) and make sure the onboarding instructions are followed.


For more information about the client analyzer, see [Troubleshoot sensor health using Microsoft Defender for Endpoint Client Analyzer](/microsoft-365/security/defender-endpoint/overview-client-analyzer).

## Registering domain joined computers with Azure Active Directory  
To successfully register devices to Azure Active Directory, you'll need to ensure the following: 

- Computers can authenticate with the domain controller 
- Computers have access to the following Microsoft resources from inside your organization's network:
  - https://enterpriseregistration.windows.net
  - https://login.microsoftonline.com
  - https://device.login.microsoftonline.com
- Azure AD connect is configured to sync the computer objects. By default, computer OUs are in Azure AD connect sync scope. If the computer objects belong to specific organizational units (OUs), configure the OUs to sync in Azure AD Connect. To learn more about how to sync computer objects by using Azure AD Connect, see [Organizational unit–based filtering](/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering#organizational-unitbased-filtering).

>[!IMPORTANT]
>Azure AD connect does not sync Windows Server 2012 R2 computer objects. If you need to register them with Azure AD for Security Management for Microsoft Defender for Endpoint, then you'll need to customize Azure AD connect sync rule to include those computer objects in sync scope. See [Instructions for applying Computer Join rule in Azure Active Directory Connect]().

>[!NOTE]
>To successfully complete the onboarding flow, and independent of a device's Operating System, the Azure Active Directory state of a device can change, based on the devices' initial state:<br>
>
>|      Starting Device    State     |      New Device State     |
>|---|---|
>|     Already AADJ or HAADJ    |     Remains as is    |
>|     Not AADJ or Hybrid Azure Active Directory Join (HAADJ) + Domain joined    |     Device is HAADJ'd    |
>|     Not AADJ or HAADJ + Not domain joined    |     Device is AADJ’d    |
>
>Where AADJ represents Azure Active Directory Joined and HAADJ represents Hybrid Azure Active Directory Joined.


## Troubleshoot errors from the Microsoft Defender for Endpoint portal


Through the Microsoft Defender for Endpoint portal, security administrators can now troubleshoot Security Management for Microsoft Defender for Endpoint onboarding. 


In **Endpoints > Device inventory**, the **Managed By** column has been added to filter by management channel (for example, MEM).


:::image type="content" alt-text="Image of device inventory page" source="./images/device-inventory-mde-error.png":::

To see a list of all devices that have failed the Security Management for Microsoft Defender for Endpoint onboarding process, filter the table by **MDE-Error**.

In the list, select a specific device to see troubleshooting details in the side panel, pointing to the root cause of the error, and corresponding documentation.


:::image type="content" alt-text="Image of device inventory page filtered" source="./images/secconfig-mde-error.png":::


## Run Microsoft Defender for Endpoint Client Analyzer on Windows 

Consider running the Client Analyzer on endpoints that are failing to complete the Security Management for Microsoft Defender for Endpoint onboarding flow. For more information about the client analyzer, see [Troubleshoot sensor health using Microsoft Defender for Endpoint Client Analyzer](overview-client-analyzer.md).

The Client Analyzer output file (MDE Client Analyzer Results.htm) can provide key troubleshooting information:

- Verify that the device OS is in scope for Security Management for Microsoft Defender for Endpoint onboarding flow in **General Device Details** section
- Verify that the device has successfully registered to Azure Active Directory in **Device Configuration Management Details**


    ![Image of client analyzer results](images/client-analyzer-results.png)


In the **Detailed Results** section of the report, the Client Analyzer also provides actionable guidance.

>[!TIP]
>Make sure the Detailed Results section of the report does not include any "Errors", and make sure to review all "Warning" messages.

For example, as part of the Security Management onboarding flow, it is required for the Azure Active Directory Tenant ID in your Microsoft Defender for Endpoint Tenant to match the SCP Tenant ID that appears in the reports' **Device Configuration Management Details** section. If relevant, the report output will recommend to perform this verification.

![Image of detailed results](images/detailed-results.png)


## General troubleshooting 

If you weren't able to identify the onboarded device in AAD or MEM, and did not receive an error during the enrollment, checking the registry key `Computer\\HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\SenseCM\\EnrollmentStatus` can provide additional troubleshooting information.  

:::image type="content" alt-text="Image of enrollment status." source="images/enrollment-status.png":::

The following table lists errors and directions on what to try/check in order to address the error. Note that the list of errors is not complete and is based on typical/common errors encountered by customers in the past: 


| Error Code                    | Enrollment Status                     | Administrator Actions                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|-------------------------------|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ``5-9``,``11-12``, ``26-33``  |General error                          |The device was successfully onboarded to Microsoft Defender for Endpoint. However, there was an error in the security configuration management flow. This could be due to the device not meeting [prerequisites for Microsoft Defender for Endpoint management channel](security-config-management). Running the [Client Analyzer](https://aka.ms/BetaMDEAnalyzer) on the device can help identify the root cause of the issue. If this doesn’t help, please contact support.   |
| ``13-14``,``20``,``24``,``25``|Connectivity issue                     |The device was successfully onboarded to Microsoft Defender for Endpoint. However, there was an error in the security configuration management flow which could be due to a connectivity issue. Verify that the [Azure Active Directory and Microsoft Endpoint Manager endpoints](security-config-management.md#connectivity-requirements) are opened in your firewall.                                                                                       |
| ``10``,``42``                 |General Hybrid join failure            |The device was successfully onboarded to Microsoft Defender for Endpoint. However, there was an error in the security configuration management flow and the OS failed to perform hybrid join. Use [Troubleshoot hybrid Azure Active Directory-joined devices](/azure/active-directory/devices/troubleshoot-hybrid-join-windows-current) for troubleshooting OS-level hybrid join failures.                                                                                                                               |
| ``15``                        |Tenant mismatch                        |The device was successfully onboarded to Microsoft Defender for Endpoint. However, there was an error in the security configuration management flow because your Microsoft Defender for Endpoint tenant ID doesn't match your Azure Active Directory tenant ID.Make sure that the Azure Active Directory Tenant ID from your Defender for Endpoint tenant matches the tenant ID in the SCP entry of your domain.For more details, [Troubleshoot onboarding issues related to Security Management for Microsoft Defender for Endpoint](troubleshoot-security-config-mgt.md).|
| ``16``,``17``                 |Hybrid error - Service Connection Point|The device was successfully onboarded to Microsoft Defender for Endpoint. However, Service Connection Point (SCP) record is not configured correctly and the device couldn't be joined to Azure AD. This could be due to the SCP being configured to join Enterprise DRS. Make sure the SCP record points to AAD and SCP is configured following best practices. For more information, see [Configure a service connection point](/azure/active-directory/devices/hybrid-azuread-join-manual#configure-a-service-connection-point).                                                      |
| ``18``                        |Certificate error                      |The device was successfully onboarded to Microsoft Defender for Endpoint. However, there was an error in the security configuration management flow due to a device certificate error. The device certificate belongs to a different tenant. Verify that best practices are followed when creating [trusted certificate profiles](/mem/intune/protect/certificates-trusted-root#create-trusted-certificate-profiles).                                                                                                    |
| ``36``                        |LDAP API error                         |The device was successfully onboarded to Microsoft Defender for Endpoint. However, there was an error in the security configuration management flow. Verify the network topology and ensure the LDAP API is available to complete hybrid join requests.     |
| ``37``                        |On-premise sync issue                  |The device was successfully onboarded to Microsoft Defender for Endpoint. However, there was an error in the security configuration management flow. Try again later. If that doesn't help, see [Troubleshoot object synchronization with Azure AD Connect sync](/azure/active-directory/hybrid/tshoot-connect-objectsync).|
| ``38``,``41``                 |DNS error                              |The device was successfully onboarded to Microsoft Defender for Endpoint. However, there was an error in the security configuration management flow due to a DNS error. Check the internet connection and/or DNS settings on the device. The invalid DNS settings might be on the workstation's side. Active directory requires you to use domain DNS to work properly (and not router's address). For more information, see [Troubleshoot onboarding issues related to Security Management for Microsoft Defender for Endpoint](troubleshoot-security-config-mgt.md).             |
| ``40``                        |Clock sync issue                       |The device was successfully onboarded to Microsoft Defender for Endpoint. However, there was an error in the security configuration management flow. Verify that the clock is set correctly and is synced on the device where the error occurs.    |


## Azure Active Directory Runtime troubleshooting 

### Azure Active Directory Runtime  

The main mechanism to troubleshoot Azure Active Directory Runtime (AADRT) is to collect debug traces. Azure Active Directory Runtime on Windows uses **ETW provider with ID bd67e65c-9cc2-51d8-7399-0bb9899e75c1**. ETW traces need to be captured with the reproduction of the failure (for example if join failure occurs, the traces need to be enabled for the duration of time covering calls to AADRT APIs to perform join).  

See below for a typical error in AADRT log and how to read it: 

![Image of event properties](images/event-properties.png)

From the information in the message, it's possible in most cases to understand what error was encountered, what Win32 API returned the error (if applicable), what URL (if applicable) was used and what AAD Runtime API error was encountered. 
  
 

## Instructions for applying Computer Join rule in AAD Connect 

For Security Management for Microsoft Defender for Endpoint on Windows Server 2012 R2 domain joined computers, an update to Azure AD Connect sync rule "In from AD-Computer Join" is needed. This can be achieved by cloning and modifying the rule, which will disable the original "In from AD - Computer Join" rule. Azure AD Connect by default offers this experience for making changes to built-in rules.

>[!NOTE]
>These changes need to be applied on the server where AAD Connect is running. If you have multiple instances of AAD Connect deployed, these changes must be applied to all instances. 

1. Open the Synchronization Rules Editor application from the start menu. In the rule list, locate the rule named **In from AD – Computer Join**. **Take note of the value in the 'Precedence' column for this rule.** 

    ![Image of synchronization rules editor](images/57ea94e2913562abaf93749d306dd6cf.png)

2. With the **In from AD – Computer Join** rule highlighted, select **Edit**. In the **Edit Reserved Rule Confirmation** dialog box, select **Yes**. 

   ![Image of edit reserved rule confirmation](images/8854440d6180a5580efda24110551c68.png)

3. The **Edit inbound synchronization rule** window will be shown. Update the rule description to note that Windows Server 2012R2 will be synchronized using this rule. Leave all other options unchanged except for the Precedence value. Enter a value for Precedence that is higher than the value from the original rule (as seen in the rule list).  

   ![Image of confirmation](images/ee0f29162bc3f2fbe666c22f14614c45.png)

4.  Select **Next** three times. This will navigate to the 'Transformations' section of the rule. Do not make any changes to the 'Scoping filter' and 'Join rules' sections of the rule. The 'Transformations' section should now be shown. 

    ![Image of inbound synchornization rule](images/296f2c2a705e41233631c3784373bc23.png)

5. Scroll to the bottom of the list of transformations. Find the transformation for the **cloudFiltered** attribute. In the textbox in the **Source** column, select all of the text (Control-A) and delete it. The textbox should now be empty. 

6. Paste the content for the new rule into the textbox. 


    ```command
    IIF(
      IsNullOrEmpty([userCertificate])
      || 
      (
        (InStr(UCase([operatingSystem]),"WINDOWS") > 0)
        && 
        (Left([operatingSystemVersion],2) = "6.")
        &&
        (Left([operatingSystemVersion],3) <> "6.3")
      )
      ||
      (
        (Left([operatingSystemVersion],3) = "6.3") 
        &&
        (InStr(UCase([operatingSystem]),"WINDOWS") > 0)
        &&
        With(
          $validCerts,
          Where(
            $c, 
            [userCertificate], 
            IsCert($c) && CertNotAfter($c) > Now() && RegexIsMatch(CertSubject($c), "CN=[{]*" & StringFromGuid([objectGUID]) & "[}]*", "IgnoreCase")),
          Count($validCerts) = 0)
      ),
      True,
      NULL
    )

    ```

7.  Select **Save** to save the new rule.

## Related topic
- [Manage Microsoft Defender for Endpoint on devices with Microsoft Endpoint Manager](/mem/intune/protect/mde-security-integration)
