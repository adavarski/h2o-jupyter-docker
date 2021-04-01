### H2O + Jupyter notebooks examples 

#### Use docker-compose to run a H2O container(standalone service) and a jupyter container that can connect to it:
```
docker-compose up -d 
```

Copy the jupyter url into your browser (from bellow cmd output):
```
docker logs jupyter-h2o
```

Run test notebook contains an H2O example ([AutoML example](https://github.com/adavarski/h2o-jupyter-automl-docker/blob/main/h2o-AutoML-example.ipynb), [H2O course on Coursera: notebooks](https://github.com/adavarski/h2o-jupyter-automl-docker/tree/main/notebooks)).

Note: You can go to http://localhost:54321 in order to use H2O Flow (H2O Flow is an open-source user interface for H2O. It is a web-based interactive environment that allows you to combine code execution, text, mathematics, plots, and rich media in a single document.)

Clean:
```
docker-compose down
```
#### Use k8s to run H2O cluster and a jupyter container that can connect to it.

Install k3s
k3s is "Easy to install. A binary of less than 40 MB. Only 512 MB of RAM required to run." this allows us to utilized Kubernetes for managing the Gitlab application container on a single node while limited the footprint of Kubernetes itself.
```
$ curl -sfL https://get.k3s.io | sh -
$ sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/k3s-config
$ export KUBECONFIG=~/.kube/k3s-config
```
Build custom JupyterLab docker image and pushing it into DockerHub container registry.
```
$ cd ./k8s/h2o-Dockerfile/
$ docker build -t jupyterlab-h2o .
$ docker tag jupyterlab-h2o:latest davarski/jupyterlab-h2o:latest
$ docker login 
$ docker push davarski/jupyterlab-h2o:latest

```
Run Jupyter Notebook inside k8s as pod:
```
$ cd ./k8s/k8s-jupyterlab
$ sudo k3s crictl pull davarski/jupyterlab-h2o:latest
$ kubectl apply -f jupyter-notebook.pod.yaml -f jupyter-notebook.svc.yaml 
```
Once the Pod is running, copy the generated token from the pod output logs.
```
$ kubectl logs jupyter-notebook
[I 06:44:51.680 LabApp] Writing notebook server cookie secret to /home/jovyan/.local/share/jupyter/runtime/notebook_cookie_secret
[I 06:44:51.904 LabApp] Loading IPython parallel extension
[I 06:44:51.916 LabApp] JupyterLab extension loaded from /opt/conda/lib/python3.7/site-packages/jupyterlab
[I 06:44:51.916 LabApp] JupyterLab application directory is /opt/conda/share/jupyter/lab
[W 06:44:51.920 LabApp] JupyterLab server extension not enabled, manually loading...
[I 06:44:51.929 LabApp] JupyterLab extension loaded from /opt/conda/lib/python3.7/site-packages/jupyterlab
[I 06:44:51.929 LabApp] JupyterLab application directory is /opt/conda/share/jupyter/lab
[I 06:44:51.930 LabApp] Serving notebooks from local directory: /home/jovyan
[I 06:44:51.930 LabApp] The Jupyter Notebook is running at:
[I 06:44:51.930 LabApp] http://(jupyter-notebook or 127.0.0.1):8888/?token=1efac938a73ef297729290af9b301e92755f5ffd7c72bbf8
[I 06:44:51.930 LabApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 06:44:51.933 LabApp] 
    
    To access the notebook, open this file in a browser:
        file:///home/jovyan/.local/share/jupyter/runtime/nbserver-1-open.html
    Or copy and paste one of these URLs:
        http://(jupyter-notebook or 127.0.0.1):8888/?token=1efac938a73ef297729290af9b301e92755f5ffd7c72bbf8
```

Port-forward the jypyter-notebook Pod with the following command: kubectl port-forward jupyter-notebook 8888:8888 
Browse to http://localhost:8888/?token=1efac938a73ef297729290af9b301e92755f5ffd7c72bbf8 


Note: Jupyter Notebooks are a browser-based (or web-based) IDE (integrated development environments)


### Deploy H2O cluster
```
kubectl apply -f ./k8s/k8s-h2o/40-h2o-statefulset.yaml
kubectl apply -f ./k8s/k8s-h2o50-h2o-headless-service.yaml
```

Example H2O AutoML jupyter notebook: https://github.com/adavarski/h2o-jupyter-docker/blob/main/k8s/notebooks/h2o-automl.ipynb


[Coursera-examples](https://github.com/adavarski/h2o-jupyter-docker/tree/main/notebooks)

