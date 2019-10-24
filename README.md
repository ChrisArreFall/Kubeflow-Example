# Kubeflow
Kubeflow is a free and open-source software platform developed by Google and first released in 2018. Kubeflow is designed to develop machine learning applications e.g. using TensorFlow and to deploy these to Kubernetes.

# Steps
# 1. Install kfctl
kfctl is a binary developed by the Kubeflow community that can be used to setup the standard set of Kubeflow components. The instructions below will download the binary as a compressed file and expand it into the current directory. Finally, we'll add kfctl to the PATH.
```sh
$ export OPSYS=linux
$ curl -s https://api.github.com/repos/kubeflow/kubeflow/releases/latest | grep browser_download | grep $OPSYS | cut -d '"' -f 4 | xargs curl -O -L &&  tar -zvxf kfctl_*_${OPSYS}.tar.gz
$ export PATH=$PATH:$PWD
```
## 2. Install Kubeflow
Now that you have the kfctl binary installed, you can install Kubeflow using the standard set of components. The script below will create a directory, "kf-poc", which will store all the kubernetes kustomize yaml files. These files are then applied to the MicroK8s cluster.
```sh
$ export KFAPP="kf-poc"
$ export VERSION=`curl -s https://api.github.com/repos/kubeflow/kubeflow/releases/latest |    grep tag_name | head -1 |    cut -d '"' -f 4`
$ export CONFIG="https://raw.githubusercontent.com/kubeflow/kubeflow/${VERSION}/bootstrap/config/kfctl_k8s_istio.yaml"
$ kfctl init ${KFAPP} --config=${CONFIG} -V
$ cd ${KFAPP}
$ kubectl create namespace kubeflow-anonymous
$ kfctl generate all -V
$ kfctl apply all -V
```

## 3. Check the install status
To see the install status of all the Kubeflow components, you can run this command:
```sh
$ kubectl -n kubeflow get po
```
Once all pods have a Running status you can exit the watch command.
```sh
$ watch -c -n 10 kubectl -n kubeflow get po
```

## 4. Access the Kubeflow Dashboard
The default nodeport should be 31380, but to confirm, run the following command - it will highlight the http port to use. You will use this in conjunction with the IP address of the VM:

```sh
$ echo `kubectl get svc -n istio-system istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}'`
```










