Sửa lỗi không nhận usb boot hoặc boot sai hệ điều hành MBR, GPT
------------

- - - 
Legacy --> MBR
- - -

- Devices -> USB Setup -> Enable (all)
- Security -> Secure Boot -> Disable
- Startup
  - CSM -> Enable
  - Boot Mode -> Legacy

Save -> F10

- - -
UEFI -> GPT
- - -

- Devices -> USB Setup -> USB Legacy Support -> Disable
- Security -> Secure Boot -> Secure Boot -> Enable
- Startup
  - Boot Mode -> UEFI
  - CSM -> Disable



