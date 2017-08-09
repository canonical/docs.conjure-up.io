Title: Kubernetes and AWS Cloud native integration

# Kubernetes and AWS CNI

The two Kubernetes spells, Kubernetes Core and the Canonical Distribution of
Kubernetes, in **conjure-up** have support for native integration with AWS. If
enabled, this allows the Kubernetes Controller Manager to automatically
provision the AWS resources that it needs to integrate with the Elastic Load
Balancer or Elastic Block Storage for persistent volume storage.

## Prerequisites

In addition to the EC2 privileges needed to deploy the master and workers, the
AWS credentials given to the spell must also be able to add and modify IAM
policies, roles, and security groups related to the ELB and EBS services, as
well as managing tags for the master and workers’ units and groups. It is
recommended that you use credentials with the **AdministratorAccess** policy. If
these permissions are available when deploying to AWS, the native integration
will be automatically enabled. At the end of the deployment, conjure-up will
report whether the integration has been enabled.

## How to Setup ELB for Kubernetes

Once a Kubernetes spell has been deployed and reported that native integration
is enabled, you can deploy a pod and expose it through the ELB. For example,
first deploy the hello-world example from Google:

```
$ kubectl run hello-world --replicas=5 --labels="run=load-balancer-example" --image=gcr.io/google-samples/node-hello:1.0  --port=8080
```

Then, expose it via the LoadBalancer:

```
$ kubectl expose deployment hello-world --type=LoadBalancer --name=hello
```

After a bit of time, the ELB endpoint should show up in `kubectl get svc -o wide`:

```
NAME                   CLUSTER-IP       EXTERNAL-IP     PORT(S)          AGE       SELECTOR
default-http-backend   10.152.183.165   <none>       80/TCP           1h        app=default-http-backend
hello                  10.152.183.202   ...-.....elb.amazonaws.com  8080:31870/TCP   1m        run=load-balancer-example
kubernetes             10.152.183.1     <none>      443/TCP          1h        <none>
```

After a few minutes, that ELB endpoint will be available via DNS and you can
view the example application in your browser:

![Hello World][hello-world]

## How to Setup EBS for Kubernetes

Once a Kubernetes spell has been deployed and reported that native integration
is enabled, you can deploy a pod with a persistent storage volume using EBS. For
example, to deploy a busybox pod that uses EBS, you will first need to create an
EBS StorageClass:

```
$ cat > sc-aws-ebs.yaml <<EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: ebs-1
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp2
EOF
$ kubectl create -f sc-aws-ebs.yaml
```

Next, you will need to create a PersistentVolumeClaim with that StorageClass:

```
$ cat > pvc-ebs.yaml <<EOF
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: my-test-claim-ebs
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi
  storageClassName: ebs-1
EOF
$ kubectl create -f pvc-ebs.yaml
```

And finally, you can deploy the busybox pod with that volume claim:

```
$ cat > busybox-with-pvc.yaml <<EOF
apiVersion: v1
kind: Pod
metadata:
  name: busybox
  namespace: default
spec:
  containers:
    - image: busybox
      command:
        - sleep
        - "3600"
      imagePullPolicy: IfNotPresent
      name: busybox
      volumeMounts:
        - mountPath: "/pv"
          name: testvolume
  restartPolicy: Always
  volumes:
    - name: testvolume
      persistentVolumeClaim:
        claimName: my-test-claim-ebs
EOF
$ kubectl create -f busybox-with-pvc.yaml
```

Once that is running, you should be able to launch the pod, write data to `/pv`,
delete the pod, recreate the pod, and verify that the data is still available:

```
$ kubectl exec -it busybox -- /bin/sh -c ‘echo “test” > /pv/test’
$ kubectl delete pod busybox --now
$ kubectl create -f busybox-with-pvc.yaml
$ kubectl run -it busybox -- /bin/sh ‘cat /pv/test’
test
```

## Troubleshooting

- If conjure-up reports “Unable to enable native integration,” check `~/.cache/conjure-up/<spell>/steps/step-03_enable-cni.err` log and AWS credential permissions
- Look for pods stuck in non-running state (`kubectl get po --all-namespaces`). For any pods not running:
    - Check pod event log: `kubectl describe po <pod-name>`
    - `kubectl logs <pod-name>`
- Check kubelet for errors on kubernetes-worker units: `journalctl -u snap.kubelet.daemon`
- Check kube-controller-manager logs (`journalctl -u snap.kube-controller-manager.daemon` on **kubernetes-master/0**)
- For EBS related issues:
    - `kubectl describe sc <StorageClass-name>`
    - `kubectl describe pvc <PersistentVolumeClaim-name>`


<!-- IMAGES -->
[hello-world]: ../../media/cni/hello-world-ex.png
