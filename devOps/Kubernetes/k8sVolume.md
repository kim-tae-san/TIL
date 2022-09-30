# PVC, PV 완전삭제

    kubectl patch pv <pv_name> -p '{"metadata": {"finalizers": null}}'
    kubectl delete pv <pv_name> --grace-period=0 --force