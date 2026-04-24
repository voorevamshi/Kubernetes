### AWS EKS & Spring Boot Identity Integration

To securely connect your Spring Boot application to AWS S3 and SQS without managing static credentials, follow this workflow:

| Step | Action | Description / Responsibility |
| :--- | :--- | :--- |
| **1** | **Create IAM Policy** | Define specific S3/SQS permissions (e.g., `s3:PutObject`, `sqs:ReceiveMessage`). |
| **2** | **Create IAM Role** | Attach the policy and configure a **Trust Relationship** for your EKS Cluster's OIDC provider. |
| **3** | **Create K8s Service Account** | Add the annotation: `eks.amazonaws.com/role-arn: arn:aws:iam::ACCOUNT_ID:role/ROLE_NAME`. |
| **4** | **Update Deployment** | In your YAML, set `spec.template.spec.serviceAccountName` to the name of your Service Account. |
| **5** | **Spring Boot App** | Use `S3Client` or `SqsClient` via Spring Cloud AWS. The SDK automatically detects the OIDC identity. |
---
### 1. The "Architectural Awareness" Approach

In my current role, our Architecture/DevOps team handles the resource creation for security reasons, but I work closely with them to define the **IAM requirements**. 
For example, when I build a Spring Boot service that needs S3 access, I specify the least-privilege actions needed (like `s3:PutObject`) and ensure our **Kubernetes Service Account** is correctly annotated to link with the **IAM Role** via **IRSA (IAM Roles for Service Accounts)**.

### 2. Explain the "How" (The Technical "Hook")

 **Web Identity Token**. This is the "secret sauce" of EKS security.

I'm familiar with the underlying mechanism: EKS leverages an **OIDC Identity Provider**. When we associate an IAM Role with a K8s Service Account, AWS injects a token into the Pod. My Spring Boot application uses the **DefaultCredentialsProvider**, which automatically detects that token to authenticate without me ever needing to manage static Access Keys.


Since your architecture team handles the IAM roles, your primary responsibility as a developer is to ensure your Kubernetes **Deployment YAML** and your **Spring Boot configuration** are "wired" correctly to use the identity they provided.

Here is exactly what you need to provide on the application side:

### 1. Kubernetes Deployment YAML

You must tell Kubernetes which **Service Account** to use. This is the "bridge" to AWS permissions.

YAML

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-spring-boot-app
spec:
  template:
    spec:
      # THIS IS THE CRITICAL LINE
      serviceAccountName: spring-boot-s3-sqs-sa 
      containers:
        - name: app
          image: my-registry/my-app:latest
          # No AWS_ACCESS_KEY_ID needed here!

```

----------

### 2. Spring Boot `pom.xml` (Dependencies)

To make the connection seamless, use the **Spring Cloud AWS** starter. It is designed to automatically look for the EKS credentials.

XML

```
<dependency>
    <groupId>io.awspring.cloud</groupId>
    <artifactId>spring-cloud-aws-starter-s3</artifactId>
</dependency>
<dependency>
    <groupId>io.awspring.cloud</groupId>
    <artifactId>spring-cloud-aws-starter-sqs</artifactId>
</dependency>

```


### 3. Spring Boot `application.yml`

Since EKS provides the credentials via the Service Account, your configuration becomes very clean. You only need to specify the **Region**.
```yaml
spring:
  cloud:
    aws:
      region:
        static: us-east-1  # Replace with your region
      # Spring Cloud AWS automatically uses "DefaultCredentialsProvider"
      # which is exactly what EKS IRSA requires.
      credentials:
        instance-profile: false # Not using EC2 roles
```

### 4. How it works in your Code

Because of the setup above, you don't need to write manual "Authentication" code. You can simply `@Autowire` the clients:

```java
@Service
public class MyService {

    private final S3Template s3Template;
    private final SqsTemplate sqsTemplate;

    public MyService(S3Template s3Template, SqsTemplate sqsTemplate) {
        this.s3Template = s3Template;
        this.sqsTemplate = sqsTemplate;
    }

    public void uploadFile(String bucket, String key, InputStream data) {
        s3Template.upload(bucket, key, data);
    }
}
```
### Summary for your Interview

If an interviewer asks how you configured your app, you can say:

On the application side, I simply ensured my **Deployment YAML** referenced the correct **Service Account**. In my Spring Boot app, I used **Spring Cloud AWS starters**, which default to the **DefaultCredentialsProvider**. This allowed the app to automatically pick up the **Web Identity Token** provided by EKS IRSA without needing any hardcoded secrets.
If an interviewer asks how you configured your app, you can say:

On the application side, I simply ensured my **Deployment YAML** referenced the correct **Service Account**. In my Spring Boot app, I used **Spring Cloud AWS starters**, which default to the **DefaultCredentialsProvider**. This allowed the app to automatically pick up the **Web Identity Token** provided by EKS IRSA without needing any hardcoded secrets.
