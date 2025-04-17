1/ Chuẩn bị file "VMware-VMvisor-Installer-6.7.0-8169922.x86_64.iso"

2/  Mặc định khi cài đặt ESXI lên máy tính sẽ có vấn đề về driver mạng. Kiểm tra driver hiện có trên máy để tải về driver mạng phù hợp với máy tính

Bấm Win > Search "Device Manager" > "Network adapters" 

![image](https://github.com/user-attachments/assets/8b94e10e-1740-47f6-964b-c8f5a2349f8c)

Bấm chuột phải và card mạng > "Properties" > "Details" > "Property" > "Hardware Ids" 

![image](https://github.com/user-attachments/assets/dc6846c3-9ff8-41ac-ac23-d3eab3493456)

Trong trường hợp này card mạng của máy có tên là "PCI\VEN_10EC&DEV_8168&SUBSYS_E0001458&REV_15" -> Realtek 8168

3/ Lựa chọn driver card mạng tương ứng (Khuyến nghị nên sử dụng trình duyệt web Microsoft Edge)

NIC drivers: https://vibsdepot.v-front.de/wiki/index.php/List_of_currently_available_ESXi_packages

![image](https://github.com/user-attachments/assets/6be0a06d-9eb7-4a7b-9a1e-84f936a51cb1)

Bấm lựa chọn "net55-r8168"

Kéo xuống cuối trang chọn "Direct Download links" > "VIB File of version 8.045a" 

Link tải: "http://vibsdepot.v-front.de/depot/RTL/net55-r8168/net55-r8168-8.045a-napi.x86_64.vib"

![image](https://github.com/user-attachments/assets/14645c62-f854-44ba-ae63-6426828029b5)

4/ Thêm driver card mạng này vào esxi iso thì ta sẽ làm cách sau: sử dụng "ESXI-Customizer"

![image](https://github.com/user-attachments/assets/33512be1-bdca-486e-b0c5-c5febada2a68)

Mở thư mục "ESXi-Customizer.cmd" dưới quyền "Run as administrator"

![image](https://github.com/user-attachments/assets/5dd9e303-0687-4380-9788-fa403358b260)

- Select the original VMware ESXi ISO: > Browse ... > Chọn file "ESXi_6.7-x86_64.iso" vừa tải về
- Select an OEM.tgz file, a VIB file or an Offine Bundle > Browse > Chọn file "net55-r8168-8.045a-napi.x86_64.vib" vừa tải về
- Select the working directory (needs to be on a local NTFS-fomatted drive): > Browse > Chọn thư mục lưu trữ
- Bỏ tích chọn "Enable automatic update check (requires working Internet connection).
- Click Run!

![image](https://github.com/user-attachments/assets/8f94e559-9b05-481d-93f7-26cfac06d29e)

Kết quả

![image](https://github.com/user-attachments/assets/2ece7191-5fa9-412f-946d-75d2f92d14c0)

5/ Tải Rufus về để tạo Usb boot

- Partion scheme : GPT
- Target system : UEFI (non CSM)
- File system : FAT32 (Default)

6/ BIOS 

- Tắt Secure Boot
- Chọn USB Boot: UEFI: SanDisk Cruzer Blade 1.26, Partition 1 (7633MB)


---------
Kiểm tra card mạng trên PC cài WinPE
------------


1/ Win + R > CMD

2/ Nhập 

```
devmgmt.msc
```

