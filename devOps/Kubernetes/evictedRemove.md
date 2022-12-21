# Evicted 상태의 파드 일괄삭제


kubectl -n argo-app delete pods --field-selector=status.phase=Failed