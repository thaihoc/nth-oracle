# Hướng dẫn cài Oralce 19c bằng Podman

Đăng ký tài khoản nếu chưa có: https://container-registry.oracle.com

Tạo Auth Token. VD: `ZgfF891vD5q+7pLVO`

Login vào Oracle Registry:

```bash
echo "ZgfF891vD5q+7pLVO" | podman login container-registry.oracle.com -u "<your-oracle account>" --password-stdin
```

Pull Oracle database image:

```bash
podman pull container-registry.oracle.com/database/enterprise:19.3.0.0
```

Tạo Volumn trên podman để lưu dữ liệu:

```bash
podman volume create oracle19c
```

Tạo network nếu chưa có:

```bash
podman network create nth
```

Run Oracle database:

```bash
podman run -d --network nth
  --name oracle19c
  -p 1521:1521 -p 5500:5500
  -e ORACLE_SID=ORCLCDB
  -e ORACLE_PDB=ORCLPDB1
  -e ORACLE_PWD=hocnt123
  -e ORACLE_CHARACTERSET=AL32UTF8
  -e ENABLE_ARCHIVELOG=true
  -v oracle19c:/opt/oracle/oradata
  container-registry.oracle.com/database/enterprise:19.3.0.0
  ```
Đăng nhập thử bằng tài khoản sys:

```bash
podman exec -it oracle19c sqlplus sys/hocnt123@ORCLCDB as sysdba
```

## Tham khảo

Đặt lại mật khẩu cho các user DBA:

```bash
podman exec oracle19c ./setPassword.sh hocnt123
```