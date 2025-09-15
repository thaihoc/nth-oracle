# Hướng dẫn cài Oracle 19c bằng Podman

Đăng ký tài khoản Oracle tại trang Oracle Registry nếu chưa có: https://container-registry.oracle.com

Đăng nhập vào trang Oracle Registry, tạo Auth Token. VD: `ZgfF891vD5q+7pLVO`

Mở Podman và chạy lệnh sau để Podman xác thực với Oracle Registry:

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
Như vậy bạn đã cài xong Oracle 19c bằng Podman. Đăng nhập thử bằng tài khoản sys:

```bash
podman exec -it oracle19c sqlplus sys/hocnt123@ORCLCDB as sysdba
```

## Tham khảo

#### Đặt lại mật khẩu cho các user DBA

```bash
podman exec oracle19c ./setPassword.sh hocnt123
```

#### Kiểm tra để chắc chắn rằng Oracle đã chạy Archive Log mode

Chạy SQL kiểm tra:

```sql
ARCHIVE LOG LIST;
```

Kết quả:

```
Database log mode              Archive Mode
Automatic archival             Enabled
Archive destination            /opt/oracle/oradata/ORCLCDB/archive_logs
Oldest online log sequence     6
Next log sequence to archive   8
Current log sequence           8
```
Hoặc sử dụng lệnh:

```sql
SELECT LOG_MODE FROM V$DATABASE;
```

Kết quả:

```
LOG_MODE
------------
ARCHIVELOG
```
