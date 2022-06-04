# Minio

## Secrets

ExternalCertSecret is for passing user-minted TLS certificates to MinIO pods.

Similarly, credsSecret defines the MinIO root access key and secret key. IIRC, CONSOLE_ACCESS_KEY and CONSOLE_SECRET_KEY are the credentials Console uses to access the MinIO cluster. I believe the idea here is that you shouldn't really be using the root credentials for normal operations. So they are distinct users.

Sources:

- <https://github.com/minio/operator/issues/593>
- <https://github.com/minio/operator/blob/master/docs/crd.adoc#k8s-api-github-com-minio-operator-pkg-apis-minio-min-io-v2-tenantspec>

## Bucket policies

Create user first

Create Policy like

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
          "s3:*"
      ],
      "Effect": "Allow",
      "Resource": [
          "arn:aws:s3:::{{ BUCKET_NAME }}/*"
      ],
      "Sid": ""
    }
  ]
}
```

Sources:

- <https://docs.min.io/docs/minio-multi-user-quickstart-guide.html>
- <https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-bucket-policies.html>

## Upload/Sync to Minio with RClone

First the config needs to be set up

``` bash
rclone config
```

Next we set env_auth to *true* instead of saving credentials in our config file. Now we need to set env variables:

``` bash
export AWS_ACCESS_KEY={{ SUPERSAVEACCKEY }}
export AWS_SECRET_KEY={{ SUPERSAVESECRETKEY }}
```

Sources:

- <https://rclone.org/s3/#authentication>
- <https://rclone.org/s3/#minio>
- <https://rclone.org/install/#install-with-docker>

## Sync data with Minio client

``` bash
mc mirror --remove --preserve $MINIO_ENV/<bucket> $BACKUPS_DIR/$BACKUP_NAME
```

## Kubernetes

### How To ingress to console

It seems one needs both console and browser to work efficiently. In addition the minio redirect url MINIO_BROWSER_REDIRECT_URL needs to be set

Sources:

- <https://docs.min.io/docs/minio-quickstart-guide.html>

### Connect to Minio

``` bash
wget https://dl.min.io/client/mc/release/linux-amd64/mc
chmod +x mc
```

Set alias (use port 443 for HTTPS!!!!)

``` bash
./mc alias set {{ MY_MINIO }} https://{{ MINIO_URL }}:443 {{ USER }} {{ SECRET_KEY }}
./mc ls minio
./mc cp {{ SOURCE_FILE_PATH }} {{ SINK_FILE_PATH }}
```

Sources:

- <https://docs.min.io/docs/minio-client-quickstart-guide.html>
- <https://docs.min.io/docs/minio-quickstart-guide.html>

### Upload to minio with curl

``` bash
# Usage: ./minio-upload my-bucket my-file.zip
bucket=$1
file=$2
host=${S3_HOST:-$minio_host}
s3_key=${S3_KEY:-$access_key}
s3_secret=${S3_SECRET:-$secret}
resource="/${bucket}/${file}"
content_type="application/octet-stream"
date='date -R'
_signature="PUT\n\n${content_type}\n${date}\n${resource}"
signature='echo -en ${_signature} | openssl sha1 -hmac ${s3_secret} -binary | base64'
curl -X PUT -T "${file}" \
        -H "Host: ${host}" \
        -H "Date: ${date}" \
        -H "Content-Type: ${content_type}" \
        -H "Authorization: AWS ${s3_key}:${signature}" \
        https://${host}${resource}
```

Sources:

- <https://gist.github.com/PhilipSchmid/1fd2688ace9f51ecaca2788a91fec133>
- <https://gist.github.com/rubyman/cef14548ac4fb71762ff7ba8ab8f15d8>
- <https://docs.min.io/docs/minio-client-quickstart-guide.htmlconsole-address>
