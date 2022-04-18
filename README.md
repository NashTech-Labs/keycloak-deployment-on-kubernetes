<img src="https://www.knoldus.com/wp-content/uploads/Knoldus-logo-1.png" style="height: 90px">

# Keycloak deployment on Kubernetes 
<img src="https://upload.wikimedia.org/wikipedia/commons/2/29/Keycloak_Logo.png" style="height: 60px"> <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/39/Kubernetes_logo_without_workmark.svg/1200px-Kubernetes_logo_without_workmark.svg.png" style="height: 60px"> <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/2/29/Postgresql_elephant.svg/745px-Postgresql_elephant.svg.png" style="height: 60px">

**Keycloak** is an open-source Identity and Access management application. It can be used for authentication applications and it has many providers like google to provide sign-in from your google account. It has many security services like SSO, Social logins, Identity brokering, LDAP & active directory, and much more.


**For more information check out this blog:** https://blog.knoldus.com/how-to-deploy-keycloak-with-postgres-on-gke/
<a href="https://blog.knoldus.com/how-to-deploy-keycloak-with-postgres-on-gke/" target="blank"><img src="https://i0.wp.com/blog.knoldus.com/wp-content/uploads/2022/02/Screenshot-from-2022-02-24-20-32-07.png?resize=768%2C431&ssl=1" style="height: 100px"></a>


## Prerequisites 

* GCP project with enabled billing
* Kubernetes cluster (GKE)
* Basic Knowledge of Postgresql
* Basic Knowledge of Keycloak

## Deploy Postgresql

For GKE your user needs to have cluster-admin permissions on the cluster. This can be done with the following command:

```
kubectl create clusterrolebinding cluster-admin-binding \
  --clusterrole cluster-admin \
  --user $(gcloud config get-value account)
```

### 1. Create storageclass for dynamic storage provisioning

```
kubectl apply -f storageclass.yml
```

### 2. Create admin secrets for database

i) First, Encode your database password & username strings in base64

```
MY_DB_PASS=$(echo your_p@$$word | base64)
MY_DB_USER=$(echo your_username | base64)

echo -e "Your base64 encoded database username is: $MY_DB_USER\nYour base64 encoded database password is: $MY_DB_PASS"
```

**Output will look like this**

```
Your base64 encoded database username is: eW91cl91c2VybmFtZQo=
Your base64 encoded database password is: eW91cl9wQDM3MDQ2d29yZAo=
```

Put these two values encoded in base64 in `postgres-secrets.yml` file in place of YOUR_DATABASE_USERNAME & YOUR_DATABASE_PASSWORD

*  POSTGRES_PASSWORD: YOUR_DATABASE_PASSWORD
*  POSTGRES_USER: YOUR_DATABASE_USERNAME

```
sed -i 's@YOUR_DATABASE_PASSWORD@'"$MY_DB_PASS"'@g' postgres-secrets.yml
sed -i 's@YOUR_DATABASE_USERNAME@'"$MY_DB_USER"'@g' postgres-secrets.yml
```

```
kubectl apply -f postgres-secrets.yml
```

### 3. Create statefulset and service for postgres database

```
kubectl apply -f postgres-statefulset.yml
```

---

## Deploy Keycloak with postgres & nginx inress controller

### 1. Create secrets for Keycloak

i) First, Encode your keycloak admin password & username strings in base64

```
MY_KEYCLOAK_ADMIN_PASSWORD=$(echo your_p@$$word | base64)
MY_KEYCLOAK_ADMIN_USERNAME=$(echo your_username | base64)

echo -e "Your base64 encoded keycloak admin username is: $MY_KEYCLOAK_ADMIN_USERNAME\nYour base64 encoded keycloak admin password is: $MY_KEYCLOAK_ADMIN_PASSWORD"
```

**Output will look like this**

```
Your base64 encoded keycloak admin username is: eW91cl91c2VybmFtZQo=
Your base64 encoded keycloak admin password is: eW91cl9wQDM3MDQ2d29yZAo=
```

Put these two values encoded in base64 in `keycloak-sec.yml` file in place of YOUR_KEYCLOAK_ADMIN_USERNAME & YOUR_KEYCLOAK_ADMIN_PASSWORD

*  KEYCLOAK_ADMIN: YOUR_KEYCLOAK_ADMIN_USERNAME
*  KEYCLOAK_ADMIN_PASSWORD: YOUR_KEYCLOAK_ADMIN_PASSWORD

```
sed -i 's@MY_KEYCLOAK_ADMIN_USERNAME@'"$MY_KEYCLOAK_ADMIN_USERNAME"'@g' keycloak-sec.yml
sed -i 's@MY_KEYCLOAK_ADMIN_PASSWORD@'"$MY_KEYCLOAK_ADMIN_PASSWORD"'@g' keycloak-sec.yml
```

### 2. Change host name in ingress file 

Change the value of hostname & add your **domain** in place of <YOUR_DOMAIN> 

for example:

```
- host: auth.knoldus.com
```

### 3. Deploy keycloak

```
kubectl apply -f ./keycloak
```