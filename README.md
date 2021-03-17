### H2O AutoML example


Use docker-compose to run a H2O container(standalone service) and a jupyter container that can connect to it:
```
docker-compose up -d 
```

Copy the jupyter url into your browser (from bellow cmd output):
```
docker logs jupyter-h2o
```

Run test notebook contains an H2O AutoML example.

Note: You can go to http://localhost:54321 in order to use H2O FLOW.

Clean:
```
docker-compose down
```

