# Windows 10 Automatic Logon

<mark style="color:red;">**Caution:**</mark>** ** the procedure below involves modifications to the registry. This can be dangerous, so be sure to [backup the registry](https://support.microsoft.com/help/322756) before proceeding.

### Enable automatic logon

1. Open `netplwiz`\
   Press Win+R, type `netplwiz` into the Run window, then press Enter
2. Uncheck the box labeled `Users must enter a user name and password to use this computer.`\
   ![](<../.gitbook/assets/Annotation 2022-01-13 174840.png>)\
   **If the checkbox does not appear**, close the **User Accounts** window and proceed to the steps in the next section.
3. Press `Apply`
4. Fill the `Automatically sign in` dialog with the user name and password of the user you wish to logon automatically.
5. Press `OK`
6. Reboot your PC. The selected user should now automatically logon.

### If "Users must enter..." checkbox is hidden

If the `Users must enter a user name and password to use this computer.` does not appear in the **User Accounts** window, follow the steps below, then follow the **Enable automatic logon** steps above.

1. Open Registry Editor\
   Press Win+R, type regedit into the Run window, then press Enter
2. Navigate to the following key:\
   `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\PasswordLess\Device`
3. Set the `DevicePasswordLessBuildVersion` entry to 0\
   The default value is 2
4. Close Registry Editor

Tested on Windows 10 Pro Build 19044.1466

### Credit

* [https://answers.microsoft.com/en-us/windows/forum/all/how-to-log-into-windows-10-automatically/9ecb7fbe-b256-4686-b40b-45eb9fe8b106](https://answers.microsoft.com/en-us/windows/forum/all/how-to-log-into-windows-10-automatically/9ecb7fbe-b256-4686-b40b-45eb9fe8b106)
* [https://superuser.com/questions/1047623/enable-auto-sign-in-on-windows-10](https://superuser.com/questions/1047623/enable-auto-sign-in-on-windows-10)
