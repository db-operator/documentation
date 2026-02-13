# Templates

DB Operator is capable of producing custom templated entries in `Secrets` and `ConfigMaps` using the database data. This features is using **go templates** and can be used in a following way:

```yaml
---
# in Databases
kind: Database
spec:
  credentials:
    templates:
      - name: USER_AND_PASSWORD
        template: "{{ .Username }}-{{ .Password }}"
        secret: true
---
# or in DbUsers
kind: DbUser
spec:
  credentials:
    templates:
      - name: USER_AND_PASSWORD
        template: "{{ .Username }}-{{ .Password }}"
        secret: true
```

## Available fields and functions

These fields are available for templating:

- **Protocol**: Depends on the db engine. Possible values are `mysql`/`postgresql`
- **Hostname**: The same value as for db host in the connection configmap
- **Port**: The same value as for db port in the connection configmap
- **Database**: The same value as for db name in the creds secret
- **Username**: The same value as for database user in the creds secret
- **Password**: The same value as for password in the creds secret

So to create a full connection string, you could use a template like that:

```
"{{ .Protocol }}://{{ .Username }}:{{ .Password }}@{{ .Hostname }}:{{ .Port }}/{{ .Database }}"
```

It's also possible to use one of these functions to get data directly from data sources available to the operator:

- **Secret**: Query data from the Secret
- **ConfigMap**: Query data from the ConfigMap
- **Query**: Get data directly from the database

When using `Secret` and `ConfigMap` you can query the previously created secret to template a new one, e.g.:

```yaml
spec:
  credentials:
    templates:
      - name: TMPL_1
        template: "test"
        secret: false
      - name: TMPL_2
        template: "{{ .ConfigMap \"TMPL_1\"}}"
        secret: true
      - name: TMPL_3
        template: "{{ .Secret \"TMPL_2\"}}"
        secret: false
```

When using `Query` you need to make sure that you query returns only one value. For example:

```yaml
...
    templates:
      - name: POSTGRES_VERSION
        secret: false
        template: "{{ .Query \"SHOW server_version;\" }}"
```

For values that are shared between different databases on the same instance, it's possible to set instance variables. Once they are set on the instance, they can also be used in database/dbuser templates.


```yaml
kind: DbInstance
spec:
  instanceVars:
    TEST_KEY: TEST_VALUE
```

These variables can be also accessed by the Database/DbUser templates

```yaml
...
    templates:
      - name: DB_INSTANCE_VAR
        secret: false
        template: "{{ .InstanceVar 'TEST_KEY' }}"
...
```

Then the secret that is created by the operator should contain the following entry: `DB_INSTANCE_VAR: TEST_VALUE`. When the value is changed on the instance level, it should also trigger reconciliation of the databases and hence the values should also be updated in the target secrets.
