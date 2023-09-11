# Configure a ImageVulnerabilityScan for Grype

Below is a sample ImageVulnerabilityScan (IVS) that utilizes Grype to scan a targeted image and store the results to the specified registry location.
For addtional details about the IVS specification, refer to [Configuration Options](../scst-scan-ivs-create-your-own.html#configuration-options-1).

```yaml
apiVersion: app-scanning.apps.tanzu.vmware.com/v1alpha1
kind: ImageVulnerabilityScan
metadata:
  name: grype-ivs
  annotations:
    app-scanning.apps.tanzu.vmware.com/scanner-name: Grype
spec:
  image: TARGET-IMAGE
  scanResults:
    location: registry/project/scan-results
  workspace:
    size: WORKSPACE-STORAGE-SIZE
  serviceAccountNames:
    publisher: publisher
    scanner: scanner
  steps:
  - name: grype
    image: GRYPE-SCANNER-IMAGE
    args:
    - -o
    - cyclonedx-json
    - registry:$(params.image)
    - --file
    - /workspace/scan-results/scan.cdx.json
    env:
    - name: GRYPE_ADD_CPES_IF_NONE
      value: "false"
    - name: GRYPE_EXCLUDE
    - name: GRYPE_SCOPE
```

Where:

- `TARGET-IMAGE` is the image to be scanned.  Digest must be specified.
- `WORKSPACE-STORAGE-SIZE` is the requested amount of disk to provide to the persistent volume used by the workspace, For example: `4Gi` This value will need to be large enough to accommodate the Grype database.
- `GRYPE-SCANNER-IMAGE` is the image containing the Grype CLI. For example, `anchore/grype:latest` For additional publicly available Grype images refer to [DockerHub](https://hub.docker.com/r/anchore/grype/tags). For more information on the usage of the Grype CLI, refer to the [Grype documentation](https://github.com/anchore/grype#getting-started).
