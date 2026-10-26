Reset 120 day RDS Multi-Session (Grace period) on Window Server 2019
----

Step 1: Login account `Administrator`

Step 2: Launch Registry Editor (regedit)

Step 3: In regedit, browse to `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\GracePeriod`

Step 4: Within this registry key is a `REG_BINARY` entry whose name begins with the string L$RTMTIMEBOMB. (This is likely the only entry in this key.) The default permissions on this entry do not allow it to be modified or deleted, so these permissions must be changed

Step 5: Right-click the **GracePeriod** folder in the left pane of regedit and select **Permissions**.

Step 6: In the permissions window that appears, select **Administrators**, and assign this group **Full Control** permissions. Click **OK** to close the window.

Step 7: If an error indicates that the permissions could not be changed, take ownership of the folder by following these steps:
  - If the permissions window is no longer open, right-click the **GracePeriod** folder and select **Permissions** again.
  - Click **Advanced**.
  - At the top of the Advanced Security Settings window, Click the **Change** link next to the current owner.
  - Type **Administrators** in the blank and click **Check Names**. Confirm that the local Administrators group is listed and click **OK**.
  - Check the box labeled **Replace owner on sub containers and objects** and click **OK**.
  - Click **OK** again to close the permissions window, then attempt to change the permissions of the folder again.

Step 8: Right-click the `L$RTMTIMEBOMB...` registry entry and select **Delete**. Click **Yes** to confirm the deletion.

Step 9: Exit regedit.

Step 10: Reboot the session host server in order for the registry change to take effect.


-------------

Check Power Shell
--------------

## Kiểm tra RDS đã hết hạn chưa
Trên Windows Server 2019:
1. Mở PowerShell bằng quyền Run as Administrator.
2. Copy lệnh sau và nhấn Enter:
```
(invoke-cimmethod -inputobject (get-ciminstance -namespace root/CIMV2/TerminalServices -classname Win32_TerminalServiceSetting) -methodname GetGracePeriodDays).DaysLeft
```

Ví dụ kết quả:

```
0
```

> Điều này có nghĩa là thời gian Grace Period đã hết.

## Kiểm tra RD Session Host
```
Get-WindowsFeature RDS-RD-Server, RDS-Licensing
```

## Kiểm tra cấu hình cho phép một tài khoản có nhiều phiên RDP đã được bật hay chưa

Mở PowerShell (Administrator) và chạy:

```
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fSingleSessionPerUser
```

Nếu kết quả là `0x0`, cấu hình cho phép một tài khoản có nhiều phiên RDP đã được bật.

Sau đó chạy:
```
quser
```

## Lệnh này sẽ hiển thị những user thuộc nhóm Remote Desktop Users.
```
Get-LocalGroupMember -Group "Remote Desktop Users"
```

## Nếu user1, user2, user3, user4 chưa có trong nhóm, bạn có thể cấp quyền bằng:
```
Add-LocalGroupMember -Group "Remote Desktop Users" -Member "user1","user2","user3","user4"
```


