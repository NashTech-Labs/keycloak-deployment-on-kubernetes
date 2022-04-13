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

Change values of these two keys encoded in base64 in `postgres-secrets.yml` file

*  POSTGRES_PASSWORD: YWRtaW4K
*  POSTGRES_USER: YWRtaW4K

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

Change values of these two keys encoded in base64 in `keycloak-sec.yml` file

*  KEYCLOAK_ADMIN: YWRtaW4K
*  KEYCLOAK_ADMIN_PASSWORD: YWRtaW4K

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