# vSphere 8 with Tanzu - Bring your own CA Certificate for the workload cluster

## Enviornment tested - 
- vSphere 8U1 
- Applies to TKG Workload cluster
- apiVersion: cluster.x-k8s.io/v1beta1 or higher

---
Steps 

--DO NOT DEPLOY THE WORKLOAD CLUSTER BEFORE COMPLETING THESE STEPS.--

1. Modify the `ca.crt.template` as per your requirements. Add/modify the `[alt_name]` section. Do not change the other sections.
2. Generate the private key 
```bash
openssl genrsa -out tls.key 2048
```
3. Create the CA cert using the configuration saved in the `ca.crt.tempate` file.
```bash
openssl req -x509 -new -nodes -key tls.key -sha256 -days 3650 -out tls.crt -config ca.crt.tempate
```
4. Validate the CA cert (optional)
```bash
openssl x509 -in tls.crt -text -noout
```
5. Your working directoy should have 2 files  - `tls.key` and `tls.crt`. Base64 encode them.
```bash
base64 -w0 tls.crt
base64 -w0 tls.key
```
6. Edit the `ca.yaml` file and update the values in `{{ }}`. Note the `WORKLOAD_CLUSTER_NAME` is the name of the workload cluster you will be deploying. `VSPHERE_NAMESPACE` is the Supervisor namespace where the cluster will be deployed. 
7. While logged into the Supervisor cluster
```bash
kubectl apply -f ca.yaml
```
8. Deploy the workload cluster as usual. 