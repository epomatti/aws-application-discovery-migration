# AWS Migration

```sh
account='<your_account_id>'

aws iam create-user --user-name Migrator

cp templates/trust-policy-template.json trust-policy.json

sed -i "s/ACCOUNT_ID/$account/g" trust-policy.json

aws iam create-role --role-name 'MigrationRole' --assume-role-policy-document 'file://trust-policy.json'

aws iam attach-role-policy --policy-arn 'arn:aws:iam::aws:policy/AWSApplicationDiscoveryServiceFullAccess' --role-name 'MigrationRole'
aws iam attach-role-policy --policy-arn 'arn:aws:iam::aws:policy/AWSApplicationDiscoveryAgentAccess' --role-name 'MigrationRole'
```

Prepare the `cloud-init.sh` file. You

```sh
cp templates/cloud-init-template.yaml cloud-init.txt

aws iam create-access-key --user-name Migrator

AWS_KEY_ID=<key_id>
AWS_KEY_SECRET=<key_secret>

sed -i "s/AWS_KEY_ID/$AWS_KEY_ID/g" cloud-init.txt
sed -i "s/AWS_KEY_SECRET/$AWS_KEY_SECRET/g" cloud-init.txt
```

Create the migration source a separate cloud, or even AWS EC2 will do. Here's an example with Azure:

```sh
az group create -n 'rg-migration' -l 'brazilsouth'

# Use this VM for the Discovery Agent migration
az vm create -n 'vm1' -g 'rg-migration' --image 'UbuntuLTS' --custom-data 'cloud-init.txt'
```

You should be able to connect directly from your terminal with your standard key.

```sh
ssh <your_user>@<ip_address>
```


---

### Clean-up

```sh
az vm delete -n 'vm1' -g 'rg-migration' -y
```