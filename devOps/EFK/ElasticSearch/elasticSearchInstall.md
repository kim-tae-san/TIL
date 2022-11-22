# 설치

> namespace 

    kubectl create ns logging

> helm chart

    $ helm repo add elastic https://helm.elastic.co
    $ helm repo update

> helm show values elastic/elasticsearch > es-value.yaml

    cat <<EOF > values.yaml
    global:
        kibanaEnabled: true
    kibana:
        ingress:
            enabled: true
            hostname: kibana.ingress.thekoguryo.ml
        tls: true
    EOF
    
> helm install 

    helm install elasticsearch -f values.yaml bitnami/elasticsearch -n logging

    - kubectl port-forward --namespace logging svc/elasticsearch 9200:9200
    - http://127.0.0.1:9200/

