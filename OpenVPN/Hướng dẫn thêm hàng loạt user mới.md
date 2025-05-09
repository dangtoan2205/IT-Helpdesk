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

# Script để tạo nhiều user OpenVPN từ file add-user-list.txt

# Kiểm tra quyền root
if [[ "$EUID" -ne 0 ]]; then
  echo "Vui lòng chạy script với quyền root."
  exit 1
fi

# Kiểm tra file cấu hình server
if [[ ! -e /etc/openvpn/server/server.conf ]]; then
  echo "OpenVPN chưa được cài đặt hoặc chưa cấu hình."
  exit 1
fi

# Lấy đường dẫn file danh sách user từ cùng thư mục với script
SCRIPT_DIR="$(cd "$(dirname "$0")" && pwd)"
USER_LIST_FILE="$SCRIPT_DIR/add-user-list.txt"

# Kiểm tra file danh sách user
if [[ ! -f "$USER_LIST_FILE" ]]; then
  echo "Không tìm thấy file: $USER_LIST_FILE"
  exit 1
fi

cd /etc/openvpn/server/easy-rsa/ || exit 1

# Thư mục lưu các file .ovpn
OUTPUT_DIR="/home/ubuntu"
mkdir -p "$OUTPUT_DIR"

while IFS= read -r unsanitized_client || [[ -n "$unsanitized_client" ]]; do
  client=$(sed 's/[^0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ_-]/_/g' <<< "$unsanitized_client")
  [[ -z "$client" ]] && continue

  echo ">>> Đang xử lý client: $client"

  # Bỏ qua nếu client đã tồn tại
  if [[ -f "pki/issued/${client}.crt" ]]; then
    echo "Client $client đã tồn tại, bỏ qua."
    continue
  fi

  # Tạo chứng chỉ client
  EASYRSA_CERT_EXPIRE=3650 ./easyrsa build-client-full "$client" nopass

  # Xuất file .ovpn về /home/ubuntu
  OUTPUT="${OUTPUT_DIR}/${client}.ovpn"
  {
    cat /etc/openvpn/server/client-common.txt
    echo "<ca>"
    cat /etc/openvpn/server/easy-rsa/pki/ca.crt
    echo "</ca>"
    echo "<cert>"
    sed -ne '/BEGIN CERTIFICATE/,$ p' /etc/openvpn/server/easy-rsa/pki/issued/"$client".crt
    echo "</cert>"
    echo "<key>"
    cat /etc/openvpn/server/easy-rsa/pki/private/"$client".key
    echo "</key>"
    echo "<tls-crypt>"
    sed -ne '/BEGIN OpenVPN Static key/,$ p' /etc/openvpn/server/tc.key
    echo "</tls-crypt>"
  } > "$OUTPUT"

  chown ubuntu:ubuntu "$OUTPUT"
  echo "✅ Đã tạo file: $OUTPUT"
done < "$USER_LIST_FILE"

echo "🎉 Hoàn tất tạo toàn bộ client trong danh sách."
```
