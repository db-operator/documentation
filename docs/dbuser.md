# DbUser

**DbUsers** can be used to manage users on a database server.

When you create a `Database`, db-operator will create a main user, that full access to the created database. You should use this user for you workloads.
If you happen to need more users on your database, you can use `DbUser` Custom Resource

## How to create DbUsers

Let's have a look at the manifest:
```
apiVersion: kinda.rocks/v1beta1
kind: DbUser
metadata:
  name: mysql-readwrite
  namespace: default
spec:
  secretName: mysql-readwrite-secret
  accessType: readWrite
  databaseRef: mysql-db
```

### Secret Name

The `.secretName` we have already seen in the **Databases**, and here it's used almost in the same way. The only difference is that dbuser controller will reuse the **ConfigMap** that was created by the database controller, and will only create a **Secret**.


### Access Types

`DbUser` supports two access types:

- readWrite (SELECT, INSERT, UPDATE, DELETE)
- readOnly (SELECT)

### Database Reference

**DbUsers** are always attached to **Databases**. Once you have a **Database** that is ready, you can create a **DbUser** to grant additional access to it. Another important thing to consider, is that **DbUser** is a namespace resource, and it must live in the same namespace as a **Database**

### Credentials Templates

You can also use [templates](templates.md) to generate connection strings for users, but since it's reusing the database **ConfigMaps**, you are only allowed to write templated values to **Secrets**, so the `.secret` in the template should always be `true`

### Extra Labels and Annotations

With `credentials.metadata.extraLabels` and `credentials.metadata.extraAnnotations` you can add custom metadata to the **Secret** that stores the generated user credentials. These values are merged with the metadata created by the operator. Keys defined in `extraLabels` or `extraAnnotations` overwrite existing values with the same key.

Example:

```yaml
spec:
  credentials:
    metadata:
      extraLabels:
        environment: development
      extraAnnotations:
        reflector.v1.k8s.emberstack.com/reflection-allowed: "true"
        reflector.v1.k8s.emberstack.com/reflection-auto-enabled: "true"
        reflector.v1.k8s.emberstack.com/reflection-auto-namespaces: target-namespace
```

This metadata can be used by external controllers that watch annotations or require specific labels to enable Secret synchronization or reflection across namespaces.

## Experimental features

Experimental features are added via annotations, the following features are available for `DbUsers`

### Grant To Admin on Delete

On instances where the admin is not a super user, it might not be able to drop owned by user, so we need to grant the user to the admin.

But it's not possible on the AWS instances with the `rds_iam` roles, because then admins are not able to log in with a password anymore and the db-operator will not be able to do anything on databases.

That's where this workaround might be used. It will only grant the role to the admin when a user is being removed.
```yaml
kinda.rocks/grant-to-admin-on-delete: "true"
```

### Allow Existing User

By default the operator is trying to make sure, that the user that it's trying to maintain is also created by it. Because otherwise it would be possible to make operator believe that a **Database** user that is created by the database controller should become a **readOnly** one.

So if a user already exists on a server, when a **CR** is created, operator will not try to manage it.

If you need the operator to be able to managed existing users, you need to set the following annotation:

```yaml
kinda.rocks/allow-existing-user: "true"
```
