# Quick Start Guide

## 快速部署指南

### 前置條件檢查

1. 確保您的 K8s 集群有：
   - Traefik Ingress Controller
   - cert-manager
   - DNS: `devgradingpdf.grading.software` 指向您的 Ingress IP

### 一鍵部署

```bash
# 方式 1: 使用部署腳本（推薦）
./deploy.sh

# 方式 2: 手動部署所有檔案
kubectl apply -f .
```

### 快速驗證

```bash
# 檢查所有資源
kubectl get all -n grading-pdf-dev

# 檢查 TLS 憑證狀態
kubectl get certificate -n grading-pdf-dev

# 測試服務
curl https://devgradingpdf.grading.software/health
```

### 查看日誌

```bash
# API 日誌
kubectl logs -l app=grading-pdf-api-dev -n grading-pdf-dev -f

# Worker 日誌
kubectl logs -l app=grading-pdf-worker-dev -n grading-pdf-dev -f
```

### 更新部署

```bash
# 拉取最新鏡像並重啟
kubectl rollout restart deployment/grading-pdf-api-dev -n grading-pdf-dev
kubectl rollout restart deployment/grading-pdf-worker-dev -n grading-pdf-dev
```

### 清理

```bash
# 刪除所有資源（包含持久化資料）
./cleanup.sh
```

## 檔案結構

```
k8s/grading-pdf-dev/
├── namespace-dev.yaml      # 命名空間
├── configmap-dev.yaml      # 配置
├── redis-dev.yaml          # Redis + PVC
├── deployment-dev.yaml     # API + Worker
├── service-dev.yaml        # Service
├── ingress-dev.yaml        # Ingress + TLS
├── deploy.sh               # 部署腳本
├── cleanup.sh              # 清理腳本
├── README.md               # 完整文檔
└── QUICKSTART.md           # 本檔案
```

## 關鍵資訊

- **網域**: `https://devgradingpdf.grading.software`
- **命名空間**: `grading-pdf-dev`
- **鏡像**: `chunchiehdev/grading-pdf:master`
- **Redis**: 持久化儲存 (1Gi PVC)
- **API 副本數**: 2
- **Worker 副本數**: 2

## 常見問題

### TLS 憑證未生成

```bash
# 檢查憑證狀態
kubectl describe certificate grading-pdf-dev-tls -n grading-pdf-dev

# 檢查 cert-manager 日誌
kubectl logs -n cert-manager deploy/cert-manager -f
```

### Pod 無法啟動

```bash
# 查看 Pod 詳細資訊
kubectl describe pod <pod-name> -n grading-pdf-dev

# 查看日誌
kubectl logs <pod-name> -n grading-pdf-dev
```

### Redis 連線問題

```bash
# 從 API Pod 測試 Redis
kubectl exec -it <api-pod> -n grading-pdf-dev -- redis-cli -h redis-dev ping
# 應該返回 PONG
```

## 詳細文檔

請參閱 [README.md](./README.md) 獲取完整文檔。
