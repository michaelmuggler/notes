One of the key differences between Windows 11 home and professional, is the latter has [Group Policy Editor](https://en.wikipedia.org/wiki/Group_Policy). This graphical tool is intended for sysadmins to configure all aspects of the Windows environment.

> [!tip]
> Microsoft sells Windows 11 Pro for $200 USD, however it can sometimes be found at a discount on websites like [stacksocial.com](https://stacksocial.com/). I've also found various YouTube channels gear towards gamers that discuss websites which sell keys for cheap. Proceed with caution.

There is a way to "graft" Group Policy Editor into Windows 11 Home. I have not tested this. The tutorial is [available here](https://www.itechtics.com/enable-gpedit-windows-10-home/?expand_article=1).

With Windows 11 Pro activated, using a keyboard press `Windows` + `e`, in the textbox of the Run window type `gpedit.msc` and hit `Enter`.

---

## Disable and Uninstall OneDrive

Expand the System Tray, click on the OneDrive icon. Click on the gear icon, select the `Acocunt` tab and click on `Unlink this PC`.  From the gear icon select "Quit OneDrive". OneDrive may complain about syncing in progress, simply continue.

From File Explorer app (press `Windows` + `e` on keyboard), type `%userprofile%` in the address bar and press enter, double click on the `OneDrive` folder.

Right click on the `Desktop` folder, select `Properties`, switch to the `Location` tab and select `Restore Default`, then click `Apply` and `OK`.  Answer `Yes` to popups.

Repeat the above step for the `Documents` and `Picures` folder. 

In some cases this process may not work well. I've found it easier to manually move the folders into `%userprofile%` in Windows Explorer, then use the Registry Editor (`regedit`) to update the paths so the OS knows about it. In Registry Editor, navigate to `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders`. Modify all items where `OneDrive` is in the value.

In Group Policy Editor, double click on the following folders: `Administrative Templates` (under `Computer Configuration`), `Windows Components` and `OneDrive`.

- Double click on the `Prevent the usage of OneDrive for file storage` (this is a policy setting), select the `Enabled` radio button, click `Apply` and `OK`.
- Double click on the `Save documents to OneDrive by default` policy setting, select the `Disabled` radio button, click `Apply` and `OK`.

Using the Windows Settings app (press `Windows` + `i` on keyboard), navigate to `Apps` and `Installed Apps`, search for `OneDrive` and uninstall `Microsoft OneDrive`.

Reboot is suggested.

> [!note]
> If you do not reboot, and you open "Microsoft Store" to perform app updates, this **may** reinstall Microsoft OneDrive. If OneDrive appears in Windows Explorer, simply uninstall it again from the Windows Settings app.

---
# Customizing Windows Explorer

Open Windows Explorer, click on the "...", select "Options". Switch to the "View" tab, and check the following items in the "Advanced Settings" box:

1. Decrease space between items (compact view)
2. Display the full path in the title bar
3. Show hidden files, folders and drives (under "Hidden files and folders")
4. Use check boxes to select items
5. Show all folders (under "Navigation pane")
6. Show This PC (under "Navigation pane")

---
## Disable Widgets and Chat

Right Click on the Taskbar and select `Taskbar Settings` then toggle the switches for `Widgets` and `Chat` to off.

Using Group Policy Editor, double click on `Windows Components`, double click on `Chat` and double click on the `Configures the Chat icon on the taskbar` policy. Select `Enabled` and under options set the `State` to `Disabled`. Click on `Apply` and `OK`.

Still in Group Policy Editor, under `Windows Components` find `Widgets` and double click on the `Allow widgets` policy. Select `Disabled` and click on `Apply` and `OK`.

In Windows 11 the search bar in the Taskbar has a "Bing Chat" icon which is to invoke Microsoft's ChatGPT-based UI. This had to be disabled by gogin to Windows Settings, selecting "Privacy & Security", then under "Search permissions" turning off "Show search highlights".

---
## Miscellaneous Group Policy Settings

Under `Windows Components`, select `Cloud Content`. Apply the `Enabled` configuration for the  `Turn off cloud optimized content` policy.

Under `Windows Components` select `Search`.

- Apply `Disabled` configuration for the `Allow Cortana` and `Allow Cortana above lock screeen` policies.
- For the `Allow Cloud Search` policy, select `Enabled` configuration and set `Disable Cloud Search` for `Cloud Search Setting`.
- Apply the `Enabled` configuration for the `Don't search the web or display web results in Search` policy.
- For the `Do not allow web search`, `Enable` to policy.

Under `Windows Components` select `Edge UI`.

- Apply the `Disabled` state for `Allow edge swipe`.
- Apply the `Enabled` state for the `Disable help tips`.



---
## BitLocker

Open `Control Panel`, navigate to `System and Security`, and select `Bitlcoker Drive Encryption`. Alternatively using the search bar type `bit` and select `Manage BitLocker`.

Select `Turn on BitLocker`. Follow the wizard. Prefer `Encrypt entire disk`. Restart the machine.

BitLocker uses the TPM chip on your computers motherboard to store the Volume Master Key (VMK). The VMK is used to decrypt the Full Volume Encryption Key (FVEK) that is store in encrypted format on the encrypted disk drive. This allows for the VMK to be easily changed without re-encrypting the drive. 

However, this comes with some risk. It turns out that is someone has physical access to your computer at any time, they could intercept (i.e. man-in-the-middle) the data transmission between the TPM chip and the processor [by physically connecting to the chip](https://pulsesecurity.co.nz/articles/TPM-sniffing).

The recommended mitigation is to setup BitLocker to require a PIN. This PIN is stored and managed by the TPM. This in turn cases the TPM to not divulge the VMK unless "unlocked" via entering the PIN. The assumption here is that if your device is physically stolen the VMK will not be recoverable.

To do this, first in Group Policy Editor, under `Windows Components`, `BitLocker Drive Encryption`, and `Operating System Drives` select and `Enable` the `Require additional authentication at startup`. The options should be left to defaults.

Return to the `BitLocker Drive Encryption` page in `Control Panel` and now the `Change how drive is unlocked at setup` link should be visible. Clicking on that link, select `Enter a PIN` and follow the instructions to configure a PIN.

You may need to run `gpupdate /force` from an administrator command prompt session.

---

## Restore old-style Context Menu

Starting with Windows 11, Microsoft redesigned the context menu. This is the menu which appears when right clicking on an item in Windows Explorer for example. There are a few problems with this redesign. First, muscle memory over several generations of Windows have me looking for specific operations which are replaced with an Icon. Second, programs I use commonly like [7-Zip](https://www.7-zip.org/download.html) and [Visual Studio Code](https://code.visualstudio.com/) both add context menu items that are not visible with the redesign.

From [this tutorial](https://pureinfotech.com/bring-back-classic-context-menu-windows-11/):
1. Navigate to `HKEY_CURRENT_USER\SOFTWARE\CLASSES\CLSID` in Registry Editor.
2. Add key `{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}`.
3. Right click on the newly added key and add another key named `InprocServer32` with a blank value.
4. Restart Windows Explorer using the Task Manager.

According to [this Microsoft Community post](https://answers.microsoft.com/en-us/windows/forum/all/restore-old-right-click-context-menu-in-windows-11/a62e797c-eaf3-411b-aeec-e460e6e5a82a) it is possible to do this via Command Prompt:

```cmd
reg.exe add "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /f /ve
```

---

## Software to consider

- [7-Zip](https://www.7-zip.org/download.html)
- [RoundedTB](https://apps.microsoft.com/store/detail/roundedtb/9MTFTXSJ9M7F)
- [QuickLook](https://apps.microsoft.com/store/detail/quicklook/9NV4BS3L1H4S)
- 