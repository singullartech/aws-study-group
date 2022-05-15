## STEP 1 - config amazonlinux 2 to run docker

```sh
sudo amazon-linux-extras install docker
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -a -G docker ec2-user
```

## STEP 2 - Nginx config

```sh
mkdir /home/ec2-user/nginx-sample
```

```sh
# /home/ec2-user/nginx-sample/default.conf

server {
    listen       80;
    listen  [::]:80;
    server_name  localhost;

    # access_log /var/log/nginx/access.log poslog;

    location ~ ^/ {
      default_type application/json;
      return 200 '{"status":"succes", "hostname" : "$hostname"}';
    }
}
```

```sh
docker run --rm -v $PWD/default.conf:/etc/nginx/conf.d/default.conf -it -p 80:80 nginx
```

## STEP 3 - Run nginx using docker

```sh
docker run -h $(hostname) -d --restart unless-stopped --name nginx-sample -v /home/ec2-user/nginx-sample/default.conf:/etc/nginx/conf.d/default.conf -it -p 80:80 nginx
```

## STEP 4 - User data

```sh
#!/bin/bash
docker run -h $(hostname) -d --restart unless-stopped --name nginx-sample -v /home/ec2-user/nginx-sample/default.conf:/etc/nginx/conf.d/default.conf -it -p 80:80 nginx
```

