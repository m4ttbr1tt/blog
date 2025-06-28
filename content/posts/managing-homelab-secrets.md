+++
date = '2025-06-25T18:45:39+02:00'
draft = false
title = 'Managing homelab secrets'
authors = ["Matt Britt"]
tags = ["homelab","k3s","GitOps"]
+++


In the last post we manually created a Cloudflare secret directly using kubectl, which goes against what we are trying to achieve with GitOps...

<!--more-->

I want to be able to destroy and recreate the cluster all from one repository, so the secrets need to be stored in git, and thus we will need to store the secrets in
a secure way.

I am using sops (with age encryption) [https://github.com/getsops/sops](https://github.com/getsops/sops)

First setup sops and age, for me thats...

```shell
sudo pacman -S sops age
```

Then generate a key pair and  save to your password manager. (we will be deleting the age.agekey file after we are done)

```shell
age-keygen -o age.agekey
cat age.agekey
```

The contents of the file will look something like this...

```shell
Public key: age1j6pyg3y73pt5leldl2csjk8a369fa2xxmuanywx6mn0mfhwc4dzs0lvthv
# created: 2025-06-24T10:04:52+02:00
# public key: age1j6pyg3y73pt5leldl2csjk8a369fa2xxmuanywx6mn0mfhwc4dzs0lvthv
AGE-SECRET-KEY-13HMN4HPFLZ60ECZUHHYT5JW75WNG3AL3JCYJ36H90209PKRRYWHQ0320ZS

```

Make sure you set an environment variable with your public key:

```shell
export AGE_PUBLIC=age1j6pyg3y73pt5leldl2csjk8a369fa2xxmuanywx6mn0mfhwc4dzs0lvthv
```

Next we will generate a secret manifest that uses our points to our Cloudflare credentials file.

```shell
kubectl create secret generic tunnel-credentials \
--from-file=credentials.json=58c97568-9eda-4dbf-9857-b6cf9cf91259.json \
--dry-run=client -o yaml > cloudflare-secret.yaml
```

> Kubernetes secrets are NOT secret. We have to enrcypt the content still.

The below command references your env variable with the public key, targets the specifice section of your yaml with regex, and makes an in-place edit of the secret file.

```shell
sops --age=$AGE_PUBLIC \
--encrypt --encrypted-regex '^(data|stringData)$' --in-place cloudflare-secret.yaml
```

If you cat the file you will now see the contents of the credintials file have been encrypted in place

```yaml
apiVersion: v1
data:
    credentials.json: ENC[AES256_GCM,data:aHXoMNZSR1mzMrnWMrpwNVJ65MF3axL43OanZnmdjYKViSphDBl491s+tMhI0G3xeQiFBKcBFsHmKT7c8PaV+WgfJG7yg6J36QtJxLz7xG87d9Z5PkWfSo390tPxSr8GLpbi3Qgr+AVxvwjY3F3E8H19uFEyJQQxWDS/sTSNm/wDIdhpIsuTzqUZvVuZLlJmV4Unj5TaZUk1/divBvivShLLdPh1smFjqdH+r1f5uTb2lr6sM82U5do0A89FOOwM+yd6J2c7P9kGQJ+yMktEW0hF/gJkSYRjsB/8YEAZ+JvhEkE69Njoxtmu4jDSbZSN5Ndr13yhYWNviVZwnMUNa1x/fGv6JoA4A2VVGT65OYY4jZvl/xQBsD8uuq1YvtGz8tmGoZHIjFT8U0MGY8Mu6l5uN8sB44k8LhEHTq2+HC+34o6GqYZX7y5zQWJlrJ2NOW4fFVZCr+U6YvWr1BxCdhX6rk6GOMnZ18zWvY6LNHr+RaUH+AR50fmW+IO19NC2v3spxrSjBHH+oSmdksXVU4FVxLJskwyCbNXfcTVG9OIH3FnB3lilxvvy6b1CA5WA6Z5LXSNUxLEi99IqDjQz5SyY7LclRXOgJxz3kh4Zq0m+RaAMJTaSO4JKPoTpDtfbECYnMxYWeJhrKdOVQLNuOZWIg6S9O6CuxmOOezkq/9M=,iv:2h/BVKyWDyQIRYarBlYYraXjD2hENDyA9HfjMnu5eg0=,tag:bMC3lUKS/9gnX4BJT9Q/qQ==,type:str]
kind: Secret
metadata:
    creationTimestamp: null
    name: tunnel-credentials
sops:
    age:
        - recipient: age1j6pyg3y73pt5leldl2csjk8a369fa2xxmuanywx6mn0mfhwc4dzs0lvthv 
          enc: |
            -----BEGIN AGE ENCRYPTED FILE-----
            YWdlLWVuY3J5cHRpb24ub3JnL3YxCi0+IFgyNTUxOSBESUtsT2dvWkJFdTJCaGgr
            azNvOXl1NjFCWU1wTi8rb0RLRjhYeFM2QTFBCkdZVitlVUlCNkxvQlBWVnJZUUlG
            SDQ2R1NZMkZVY0FuVUhpT05nbkc4bUEKLS0tIEwwK3FZN1lrK05pcHhpOGlldVN2
            Sk1zdnRTdWd1QmhFZXZRWlZKMHJEQnMKyIMSF5qOq6z7AF/4vTVczlYS0P6mV3q2
            WBq/t/UVAmts0TCn8t5xv4M+EXghs1QBkr35A2ClYBYgjd7qQLSZgw==
            -----END AGE ENCRYPTED FILE-----
    lastmodified: "2025-06-28T08:23:19Z"
    mac: ENC[AES256_GCM,data:af++553y8OX+VAy7Ygg0zIYdMdEPUfwI6mSzoCqgTa2/Uqp//o94NwUicjp7ZGUmoX6BM6gK5ULd5k8Du1ncj5K5e2boFeTBk099accVeYtVwiknAWViVw4DD2Mnm9vCmDJJMNacB4Ax6i/Z0+D/MNy/076xUMyq23tqmPK98KA=,iv:LPa9LpP+iJmzrdKqpzysQdXtYHPwoHHuZqLhSIF7LiI=,tag:jU1NX0fo4mlxezAh7CourQ==,type:str]
    encrypted_regex: ^(data|stringData)$
    version: 3.10.2
```

In order for sops do decrypt our secret it needs the private key (this is a manual step when setting up your cluster)

```shell
cat age.agekey |
kubectl create secret generic sops-age \
--namespace=flux-system \
--from-file=age.agekey=/dev/stdin
```

The last thing is to amend our apps.yaml file to include sops  for decryption.

```yaml
# update spec of apps.yaml so decryption works
 decryption:
    provider: sops
    secretRef:
      name: sops-age
```

Push to git and flux will reconcile and decrypt your secret! 💥
