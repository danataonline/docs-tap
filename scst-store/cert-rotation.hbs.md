# Certificate Rotation

This topic describes TLS Certificate Rotation for Supply Chain Security Tools (SCST) - Store.

## Certificates

By default, the `use_cert_manager` setting is set to `"true"`.
When the setting `use_cert_manager` is `"true"` the Store uses `cert-manager` to generate a CA certificate, an API certificate, and a DB Certificate.

To see these certificates, run the following:

```bash
$ kubectl get certificate -n metadata-store
NAME                    READY   SECRET                  AGE
app-tls-ca-cert         True    app-tls-ca-cert         38d
app-tls-cert            True    app-tls-cert            38d
postgres-db-tls-cert    True    postgres-db-tls-cert    38d
```

Those above certificates are automatically rotated by `cert-manager`.

The Store is able to load these certificates automatically once `cert-manager` rotates them.

If the environment is a [multi-cluster](multicluster-setup.hbs.md) setup, the operator will need to manually copy over the new ca certificate to the build cluster.


## Certificate duration setting
 
In the `tap-values.yaml` file, `api_cert_duration`, `api_cert_renew_before`, `ca_cert_duration`, and `ca_cert_renew_before`  are configurable as shown in the following YAML:

```yaml
metadata_store:
  ca_cert_duration: 8760h
  ca_cert_renew_before: 1h
  api_cert_duration: 2160h
  api_cert_renew_before: 24h
```

Where `ca_cert_duration` is the duration that the ca certificate will be valid for. Must be given in h, m, or s. Default value is 8760h

Where `ca_cert_renew_before` is how long before the expiry of the ca certificate is renewed. Must be given in h, m, or s. Default value is 1h

Where `api_cert_duration` is the duration that the api certificate will be valid for. Must be given in h, m, or s. Default value is 2160h

Where `api_cert_renew_before` is how long before the expiry of the api certificate is renewed. Must be given in h, m, or s. Default value is 24h

Note:
 - The `*_cert_duration` and the corresonding `*_renew_before` settings should not be ["very close"](https://cert-manager.io/docs/usage/certificate/#renewal). This may lead to a renewal loop.

 - The `*_cert_duration` must be greater than the corresponding `*_renew_before`.

 - The above settings only take effect when `use_cert_manager` is `"true"`. If the `use_cert_manager` is not set, it will default to `"true"`.
