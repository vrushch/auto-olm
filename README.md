# Automation for Operator Lifecycle Manager, Operator installation and upgrade
### working for openshift 4.5.x and 4.6.x

## Pre-requisite:

1. ansible
```
python3 -m pip install ansible
```
2. podman
```
yum install podman
```
3. Get ibm-spectrum-scale-csi repo
```
cd /root
git clone https://github.com/IBM/ibm-spectrum-scale-csi.git

```
4. Create public Container Image Repository on quay
```
i.   Go to quay.io and login.
ii.  Click the + icon in the top right of the header on any quay.io page and choose 'New Repository'
iii. Select 'Container Image Repository' on the next page
iv.  Enter repository name , click on public and and then click the 'Create Public Repository' button.
```
5. Clone this repo
```
git clone https://github.com/vrushch/auto-olm.git
cd auto-olm
```

## Steps to Follow

1. Disable default operator sources.
```
oc patch OperatorHub cluster --type json -p '[{"op": "add", "path": "/spec/disableAllDefaultSources", "value": true}]'
```
2. Edit required values in olm-test-playbook.yaml
```
QUAY_NAMESPACE: vrushch                  # this is quay username
PACKAGE_NAME: vrushal-csi-driver-repo-2  # this is Container Image Repository Repository name

# Versions you want to test. Playbook will upload  in order and run  tests.
OPERATOR_VERSIONS:
  - 1.0.0
  - 1.1.0
  - 2.0.0
  - 2.1.0

# Quay username with write access to the application and Quay Password
QUAY_USERNAME: "vrushch"
QUAY_PASSWORD: "XXXXXXXXXX"

# Check OPERATOR_DIR location is correct
OPERATOR_DIR:  /root/ibm-spectrum-scale-csi/operator/deploy/olm-catalog/ibm-spectrum-scale-csi-operator
```
3. Run following command
```
ansible-playbook olm-test-playbook.yaml
```
4. Go to operatorhub listing of your Openshift cluster and install operator.
   verify operator installtion using  
```
oc get pod -n ibm-spectrum-scale-csi-driver
```
5. Enable default operator sources
```
oc patch OperatorHub cluster --type json -p '[{"op": "add", "path": "/spec/disableAllDefaultSources", "value": false}]'
```
6. Delete Application from quay 
```
Repositories -> repository_name -> setting -> delete repository
```
