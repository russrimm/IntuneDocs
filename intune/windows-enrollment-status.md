---
# required metadata

title: Set up an enrollment status page
titleSuffix: Microsoft Intune
description: Set up a greeting page for users enrolling Windows 10 devices.
keywords:
author: ErikjeMS
ms.author: erikje
manager: dougeby
ms.date: 10/5/2018
ms.topic: conceptual
ms.service: microsoft-intune
ms.localizationpriority: high
ms.technology:
ms.assetid: 8518d8fa-a0de-449d-89b6-8a33fad7b3eb
 
# optional metadata
 
#ROBOTS:
#audience:
#ms.devlang:
ms.reviewer: damionw
ms.suite: ems
search.appverid: MET150
#ms.tgt_pltfrm:
ms.custom: intune-azure
ms.custom: seodec18 
ms.collection: M365-identity-device-management
---


 
# Set up an enrollment status page
 
[!INCLUDE [azure_portal](./includes/azure_portal.md)]
 
The Enrollment status page displays installation information about Windows 10 devices during initial device enrollment, for example, Autopilot and also when a user uses a managed device for the first time that has the Enrollment status page policy already applied.  The Enrollment status page can help users understand the status of their device during device setup. You can create multiple enrollment status page profiles and apply them to different groups that contain users. Profiles can be set to:
- Show installation progress.
- Block usage until installation completes.
- Specify what a user can do if device setup fails.

You can also set the priority order for each profile to account for conflicting profile assignments to the same user.

> [!NOTE]
> The Enrollment status page can only be targeted to a user who belongs to an assigned group and the policy is set on the device at the time of enrollment for all users that use the device.  

## Turn on default enrollment status page for all users

To turn on the enrollment status page, follow the steps below.
 
1. In [Intune](https://aka.ms/intuneportal), choose **Device enrollment** > **Windows enrollment** > **Enrollment Status Page**.
2. In the **Enrollment Status Page** blade, choose **Default** > **Settings**.
3. For **Show app and profile installation progress**, choose **Yes**.
4. Choose the other settings that you want to turn on and then choose **Save**.

## Create enrollment status page profile and assign to a group

1. In [Intune](https://aka.ms/intuneportal), choose **Device enrollment** > **Windows enrollment** > **Enrollment Status Page** > **Create profile**.
2. Provide a **Name** and **Description**.
3. Choose **Create**.
4. Choose the new profile in the **Enrollment Status Page** list.
5. Choose **Assignments** > **Select groups** > choose the groups that you want to adopt this profile > **Select** > **Save**.
6. Choose **Settings** > choose the settings you want to apply to this profile > **Save**.

## Set the enrollment status page priority

A user might be in multiple groups and have multiple enrollment status page profiles. To deal with such conflicts, you can set the priorities for each profile. If someone has more than one enrollment status page profile, only the profile with the highest priority will be applied for the device they are enrolling at the time of enrollment.

1. In [Intune](https://aka.ms/intuneportal), choose **Device enrollment** > **Windows enrollment** > **Enrollment Status Page**.
2. Hover over the profile in the list.
3. Using the three vertical dots, drag the profile to the desired position on the list.

## Block access to a device until a specific application is installed

You can specify which apps need to be installed before the user can access the desktop.

1. In Intune, choose **Device enrollment** > **Windows enrollment** > **Enrollment Status Page**.
2. Choose a profile > **Settings**.
3. Choose **Yes** for **Show app and profile installation progress**.
4. Choose **Yes** for **Block device use until all apps and profiles are installed**.
5. Choose **Selected** for **Block device use until these required apps are installed if they are assigned to the user/device**.
 6. Choose **Select apps** > choose the apps > **Select** > **Save**.

## Enrollment status page tracking information

There are three phases where the Enrollment status page tracks information for; device preparation, device setup, and account setup.

### Device preparation

For device preparation, the enrollment status page tracks Trusted Platform Module (TPM) key attestations (when applicable), progress in joining Azure Active Directory, enrolling into Intune, and installation of Intune management extensions.

### Device setup

For device setup, the enrollment status page tracks the following items if they're assigned to All Devices:
- Security policies
    - One configuration service provider (CSP) for all enrollments.
    - Actual CSPs configured by Intune aren't tracked here.
- Applications
    - Per machine Line-of-business (LoB) MSI apps.
    - LoB store apps with installation context = Device.
    - Offline store and LoB store apps with installation context = Device.
    - Win32 applications (Windows 10 version 1903 and newer only) 
- Connectivity profiles
    - VPN or Wi-Fi profiles that are assigned to **All Devices** or a device group in which the enrolling device is a member, but only for Autopilot devices
- Certificate profiles that are assigned to **All Devices** or a device group in which the enrolling device is a member, but only for Autopilot devices

### Account setup
For account setup, the enrollment status page tracks the following items if they are assigned to the current logged in user:
- Security policies
    - One CSP for all enrollments.
    - Actual CSPs configured by Intune aren't tracked here.
- Applications
    - Per user LoB MSI apps that are assigned to All Devices, All Users, or a user group in which the user enrolling the device is a member.
    - Per machine LoB MSI apps that are assigned to All Users or a user group in which the user enrolling device is a member.
    - LoB store apps, online store apps, and offline store apps that are assigned to any of the following:
        - All Devices
        - All Users
        - a user group in which the user enrolling the device is a member with installation context set to User.
    - Win32 applications (Windows 10 version 1903 and newer only) 
- Connectivity profiles
    - VPN or Wi-Fi profiles that are assigned to All Users or a user group in which the user enrolling the device is a member.
- Certificates
    - Certificate profiles that are assigned to All Users or a user group in which the user enrolling the device is a member.

### Troubleshooting
Top questions for troubleshooting.

- Why were my applications not installed during Device setup phase during Autopilot deployment that is using Enrollment status page?
    - To guarantee applications are installed during the Device setup phase during an Autopilot deployment, first, ensure the application is selected to block access in the selected apps list.  Second, ensure you targeting of the applications to the same AAD device group your Autopilot profile was assigned to. 
    
- Why is the Enrollment status page showing for non-Autopilot deployments, for example when a user logs in for the first time on a Configuration Manager co-mgmt enrolled device?  
    - The Enrollment status page is designed to provide installation status for all enrollment methods, this includes Autopilot, Configuration Manager co-mgmt and also when any new user logs into the device that has Enrollment status page policy applied for the first time.  
    
- How can I disable the Enrollment Status if it has been configured on the device?
   - Enrollment status page policy is set on a device at the time of enrollment, to disable it you can create a custom OMA-URI setting with the following configurations:

         Name:  DisableESP (choose a name you desire)
         Description:  (enter a description)
         OMA-URI:  ./Vendor/MSFT/DMClient/Provider/MS DM Server/FirstSyncStatus/SkipUserStatusPage
         Data type:  Boolean
         Value:  True 

### Known issues
Below are known issues. 
- Disabling the ESP profile does not remove ESP policy from devices and users still get ESP when they login to device for first time. ESP policy is not removed when the ESP profile is disabled, you must deploy OMA-URI to disable the ESP, see above for instructions on how to disable ESP using OMA-URI. 
- A pending reboot will always cause a timeout. Cause of timeout is because the device needs to be rebooted in order to complete the item tracked in Enrollment status page such as an application. A reboot will cause the enrollment status page to exit and after reboot the device will not enter during Account setup after reboot.  Consider not requiring a reboot with application installation. 
- A reboot during Device setup will force the user to enter their credentials before transitioning to Account setup phase.  Cause is user credentials are not preserved during reboot. Have the user enter their credentials then the Enrollment status page can continue. 
- SCEP certificates with Windows Hello for Business policies will cause timeout because user cannot complete configuring Hello pin to allow the competition of the SCEP certificate installation.  No workaround. Fix ETA is Summer 2019. 
- Enrollment status page will always timeout during an Add work and school account enrollment on Windows 10 versions less than 1903. Cause is due to Enrollment status page waiting on Azure AD registration to complete, the issue is fixed in Windows 10 version 1903 and newer.  
- Hybrid Azure AD Autopilot deployment with ESP takes longer than the timeout duration defined in the ESP profile. On Hybrid Azure AD Autopilot deployments the ESP will take 40 minutes longer than the value set in the ESP profile to allow for on-prem AD connector to create the new device record to Azure AD. 
- Windows logon page is not pre-populate with username in Autopilot User Driven Mode.If there is a reboot during Device Setup phase of ESP the user credentials are not preserved and they will need to enter them before transitioning from Device Setup phase of ESP to the Account setup phase.  
- ESP is stuck for a very long time or never completes “Identifying” phase. The identifying phase is when Intune is computing the ESP policies, a device may never complete computing ESP policies if the current user does not have an Intune licensed assigned.  
- Configuring Windows Defender Application Control causes a prompt to reboot during Autopilot. Configuring Windows Defender Application (AppLocker CSP) requires a reboot in order to and when this policy is configured it may cause a device to reboot during Autopilot.   There is no way to suppress or postpone the reboot currently.
- When the DeviceLock policy (https://docs.microsoft.com/en-us/windows/client-management/mdm/policy-csp-devicelock) is enabled as part of an ESP profile, the autologon from OOBE and/or to the user's desktop could fail unexpectantly for two reasons.  First, if the device was not rebooted prior to the exit of ESP Device setup phase, the user may be prompted to enter in their AAD credentials instead of the autologon successfully executing and the user seeing the Windows first logged in animation. Second, if the device was rebooted prior to the exit of ESP Device setup phase (and after the user had entered in AAD credentails), the autologon will fail because the ESP Device setup phase never completed, the workaround is to reset the device.

## Next steps
After you set up Windows enrollment pages, learn how to manage Windows devices. For more information, see [What is Microsoft Intune device management?](https://docs.microsoft.com/intune/device-management)
