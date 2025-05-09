```
revoke-list.txt
```

```
toandang
abc
```

```
revoke-users.sh
```

```
#!/bin/bash

EASYRSA_DIR="/etc/openvpn/server/easy-rsa"
OUTPUT_DIR="/root"
CRL_PATH="/etc/openvpn/server/crl.pem"

cd "$EASYRSA_DIR" || {
  echo "❌ Không vào được thư mục $EASYRSA_DIR"
  exit 1
}

while IFS= read -r user; do
  echo "🔄 Đang revoke: $user"
  ./easyrsa --batch revoke "$user"
  rm -f "$OUTPUT_DIR/$user.ovpn"
  rm -f pki/issued/"$user".crt
  rm -f pki/private/"$user".key
done < /home/ubuntu/revoke-list.txt

echo "⚙️ Tạo lại CRL (Certificate Revocation List)..."
./easyrsa gen-crl

echo "📄 Copy crl.pem vào $CRL_PATH"
cp pki/crl.pem "$CRL_PATH"
chmod 644 "$CRL_PATH"

echo "✅ Hoàn tất revoke toàn bộ user trong revoke-list.txt"
```
