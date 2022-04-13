# Keycloak deployment on Kubernetes

**Keycloak** is an open-source Identity and Access management application. It can be used for authentication applications and it has many providers like google to provide sign-in from your google account. It has many security services like SSO, Social logins, Identity brokering, LDAP & active directory, and much more.

## Prerequisites 

* GCP project with enabled billing
* Kubernetes cluster (GKE)
* Basic Knowledge of Postgresql
* Basic Knowledge of Keycloak

## Deploy Postgresql

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