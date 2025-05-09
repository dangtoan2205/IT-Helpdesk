```
add-user-list.txt
```

```
toandang
abc
```

```
add-users.sh
```

```
#!/bin/bash

USER_LIST_FILE="/home/ubuntu/add-user-list.txt"  # Chỉ rõ đường dẫn tuyệt đối
EASYRSA_DIR="/etc/openvpn/server/easy-rsa"

# Kiểm tra easyrsa tồn tại
if [ ! -d "$EASYRSA_DIR" ]; then
    echo "❌ Không tìm thấy thư mục $EASYRSA_DIR"
    exit 1
fi

cd "$EASYRSA_DIR" || { echo "❌ Không thể vào thư mục $EASYRSA_DIR"; exit 1; }

# Kiểm tra tệp danh sách người dùng
if [ ! -f "$USER_LIST_FILE" ]; then
    echo "❌ Không tìm thấy tệp danh sách người dùng: $USER_LIST_FILE"
    exit 1
fi

# Tải biến môi trường nếu có
if [ -f "./vars" ]; then
    source ./vars
fi

# Tạo user
while read -r USERNAME; do
    if [[ -z "$USERNAME" ]]; then
        continue
    fi

    echo "🔧 Đang tạo VPN user: $USERNAME"

    ./easyrsa build-client-full "$USERNAME" nopass
    if [ $? -eq 0 ]; then
        echo "✅ Tạo thành công: $USERNAME"
    else
        echo "❌ Lỗi khi tạo: $USERNAME"
    fi
done < "$USER_LIST_FILE"

echo "🎉 Đã xử lý xong danh sách user."
```
