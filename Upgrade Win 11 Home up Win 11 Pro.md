How to upgrade from win 11 home to professional using command line
-----

CMD + Administrator

```
sc config licensemanager start= auto & net start licensemanager

sc config wuauserv start= auto & net start wuauserv

changepk.exe /productkey VK7JG-NPHTM-C97JM-9MPGT-3V66T
```
