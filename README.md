## Kind Cluster
`kind create cluster --config kind-cluster-config.yaml`

## Installing MinIO
Installing

    cd helms
    helm install minio -f minio/values.yaml minio/minio --namespace storage

Accessing the console

    kubectl port-forward svc/minio-console 9001:9001
    
Enter the URL: http://localhost:9001

## Installing Spark
    kubectl create namespace processing
    helm install spark-operator -f spark-operator/values.yaml spark-operator/spark-operator --namespace spark-operator --create-namespace 


## Installing Spark History Server

    1 - build hadoop image
    2 - build spark image based on hadoop image created previously
    3 - Install Spark HS by running
        helm install spark-hs -f helms/spark-history-server/values.yaml helms/spark-history-server --namespace spark-operator 