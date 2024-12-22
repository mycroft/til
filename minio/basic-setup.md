# Basic minio install

No real instruct, just enough to get minio up & running:

```sh
curl -LO https://dl.min.io/server/minio/release/linux-amd64/minio
curl -LO https://dl.min.io/client/mc/release/linux-amd64/mc

./minio server storage
API: http://127.0.0.1:9000
   RootUser: minioadmin
   RootPass: minioadmin

WebUI: http://127.0.0.1:37847
   RootUser: minioadmin
   RootPass: minioadmin

CLI: https://min.io/docs/minio/linux/reference/minio-mc.html#quickstart
   $ mc alias set 'myminio' 'http://10.0.0.6:9000' 'minioadmin' 'minioadmin'


./mc alias set testaroo http://127.0.0.1:9000 minioadmin minioadmin
Added `testaroo` successfully.

./mc mb testaroo/my-bucket
Bucket created successfully `testaroo/my-bucket`.

./mc admin user add testaroo accesskey secretkey
Added user `accesskey` successfully.

./mc admin policy attach testaroo readwrite --user accesskey
Attached Policies: [readwrite]

set -x AWS_SECRET_ACCESS_KEY secretkey
set -x AWS_ACCESS_KEY_ID accesskey
set -x AWS_ENDPOINT_URL http://127.0.0.1:9000

aws s3 ls s3://
2024-12-08 19:28:50 my-bucket

aws s3 cp /etc/passwd s3://my-bucket/passwd
upload: ../../etc/passwd to s3://my-bucket/passwd
To User: accesskey
```
