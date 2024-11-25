How to upgrade from win 11 home to professional using command line
-----

CMD + Administrator
1.
```
sc config licensemanager start= auto & net start licensemanager
```
2.
```
sc config wuauserv start= auto & net start wuauserv
```
3.
```
changepk.exe /productkey VK7JG-NPHTM-C97JM-9MPGT-3V66T
```
