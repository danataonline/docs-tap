# Configure an ImageVulnerabilityScan for Trivy

Below is a sample ImageVulnerabilityScan (IVS) that utilizes Trivy to scan a targeted image and store the results to the specified registry location.
For addtional details about the IVS specification, refer to [Configuration Options](../scst-scan-ivs-create-your-own.html#configuration-options-1).

```yaml
apiVersion: app-scanning.apps.tanzu.vmware.com/v1alpha1
kind: ImageVulnerabilityScan
metadata:
  name: trivy-ivs
  annotations:
    app-scanning.apps.tanzu.vmware.com/scanner-name: Trivy
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
  - name: trivy
    image: TRIVY-SCANNER-IMAGE
    command: ["trivy"]
    args:
    - image
    - --format
    - cyclonedx
    - --output
    - $(params.scan-results-path)/scan.cdx.json
    - --scanners
    - vuln
    - $(params.image)
```

Where
- `TARGET-IMAGE` is the image to be scanned.  Digest must be specified.
- `WORKSPACE-STORAGE-SIZE` is the requested amount of disk to provide to the persistent volume used by the workspace, For example: `4Gi` This value will need to be large enough to accommodate the Trivy database.
- `TRIVY-SCANNER-IMAGE` is the image containing the Trivy CLI. For example, `aquasec/trivy:0.42.1` For additional publicly available Trivy images, refer to [DockerHub](https://hub.docker.com/r/aquasec/trivy/tags). For more information on the usage of the Trivy CLI, refer to the [Trivy documentation](https://github.com/aquasecurity/trivy).
