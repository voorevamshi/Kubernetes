
# Keycloak Identity Provider Setup Guide

This document outlines the steps to set up the Identity and Access Management (IAM) layer for the **VMC Microservices System**. This setup provides the OAuth2/OIDC foundation for the API Gateway and downstream services.

## 1. Quick Start with Docker

To get the Keycloak server running locally on the port expected by our microservices (`9000`), run the following:

Bash

```
docker run -d \
  --name vmc-keycloak \
  -p 9000:8080 \
  -e KC_BOOTSTRAP_ADMIN_USERNAME=admin \
  -e KC_BOOTSTRAP_ADMIN_PASSWORD=admin \
  quay.io/keycloak/keycloak:latest \
  start-dev

```

-   **Console URL:** `http://localhost:9000`
    
-   **Admin Credentials:** `admin` / `admin`
    

## 2. Realm Configuration

Once logged into the Admin Console:

1.  Click the **Master** dropdown in the top-left corner.
    
2.  Select **Create Realm**.
    
3.  **Realm Name:** `vmc-realm`
    
4.  Click **Create**.
    

## 3. Client Configuration (`vmc-client`)

This client represents our microservices ecosystem.

1.  Navigate to **Clients** -> **Create client**.
    
2.  **General Settings:**
    
    -   **Client type:** `OpenID Connect`
        
    -   **Client ID:** `vmc-client`
        
3.  **Capability Config:**
    
    -   **Client Authentication:** `ON` (This makes it a Confidential Client).
        
    -   **Authorization:** `ON`.
        
    -   **Authentication Flow:** Check `Standard Flow` and `Direct Access Grants`.
        
4.  **Login Settings:**
    
    -   **Valid Redirect URIs:** `https://oauth.pstmn.io/v1/callback` (Required for Postman).
        
    -   **Web Origins:** `*` (Allows local development cross-origin requests).
        
5.  **Credentials Tab:**
    
    -   Copy the **Client Secret**. You will need this for the Postman OAuth2 handshake and potentially for service-to-service auth.
        

## 4. Test User Creation

To simulate a real user logging into the system:

1.  Go to **Users** -> **Add user**.
    
2.  **Username:** `test-user`.
    
3.  Click **Create**.
    
4.  Go to the **Credentials** tab -> **Set password**.
    
5.  Enter a password (e.g., `password123`) and toggle **Temporary** to `OFF`.
    

## 5. OAuth2 Discovery Endpoint

The microservices use the discovery endpoint to automatically fetch the Public Keys (JWKS) to verify JWT signatures.

-   **Discovery URL:** `http://localhost:9000/realms/vmc-realm/.well-known/openid-configuration`
    

## 6. Postman Integration Settings

To test the secured endpoints, use the following settings in Postman under the **Authorization** tab:

### Postman OAuth2 Configuration

To test the secured endpoints, use the following settings in Postman under the **Authorization** tab:

| Field | Value |
| :--- | :--- |
| **Grant Type** | Authorization Code |
| **Callback URL** | `https://oauth.pstmn.io/v1/callback` |
| **Auth URL** | `http://localhost:9000/realms/vmc-realm/protocol/openid-connect/auth` |
| **Access Token URL** | `http://localhost:9000/realms/vmc-realm/protocol/openid-connect/token` |
| **Client ID** | `vmc-client` |
| **Client Secret** | `<Your-Copied-Secret>` |
| **Scope** | `openid profile email` |

## 7. Production Notes (EKS)

When moving to **AWS EKS**:

-   The `issuer-uri` in `application.yml` will change from `localhost:9000` to the internal Kubernetes DNS name of your Keycloak service (e.g., `http://keycloak-service.iam-namespace.svc.cluster.local/realms/vmc-realm`).
    
-   Ensure the Keycloak service is reachable by the pods in the VPC.
    

----------

### Summary of Next Steps

1.  **Keep this file** in the root of your workspace.
    
2.  **Verify the Secret:** Every time you delete and recreate the Docker container without a persistent volume, the Client Secret will change. Always update Postman with the latest secret from the Credentials tab.
    
3.  **Troubleshooting:** If you see "Audience validation failed" in your Java logs, ensure Keycloak is adding the `aud` claim to the token, or disable audience validation in your `SecurityConfig.java`.
