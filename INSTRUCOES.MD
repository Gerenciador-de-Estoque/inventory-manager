# Instruções de Execução — Inventory Manager

Aplicação completa (frontend + backend + banco) rodando em Kubernetes local com **Kind**, atendendo aos requisitos do desafio: Deployment, StatefulSet, Service, NodePort, ConfigMap, Secret, PVC, readinessProbe e livenessProbe.

As imagens são baixadas automaticamente do Docker Hub.

## Pré-requisitos

- Docker instalado e rodando
- [Kind](https://kind.sigs.k8s.io/docs/user/quick-start/#installation) instalado
- `kubectl` instalado

---

## 1. Subir o cluster Kind

```bash
kind create cluster --name inventory
```

Verifique:
```bash
kubectl cluster-info
kubectl get nodes
```

---

## 2. Aplicar os manifests

A partir da raiz do projeto:

```bash
# 2.1 Namespace
kubectl apply -f k8s/namespace.yaml

# 2.2 ConfigMap e Secret
kubectl apply -f k8s/configmap.yaml
kubectl apply -f k8s/secret.yaml

# 2.3 PersistentVolumeClaim
kubectl apply -f k8s/pvc.yaml

# 2.4 Banco (StatefulSet + Services). Imagem postgres:17-alpine puxada do Docker Hub.
kubectl apply -f k8s/postgres-statefulset.yaml
kubectl -n inventory-manager wait --for=condition=ready pod -l app=postgres --timeout=300s

# 2.5 Backend (já roda migrations no startup). Imagem puxada do Docker Hub.
kubectl apply -f k8s/backend-deployment.yaml
kubectl -n inventory-manager wait --for=condition=ready pod -l app=backend --timeout=300s

# 2.6 Frontend. Imagem puxada do Docker Hub.
kubectl apply -f k8s/frontend-deployment.yaml
kubectl -n inventory-manager wait --for=condition=ready pod -l app=frontend --timeout=300s

# 2.7 Seed do banco (popula usuários, categorias e produtos)
kubectl apply -f k8s/seed-db.yaml
```

> O primeiro `apply` pode demorar alguns minutos enquanto o Kind faz pull das imagens do Docker Hub. Os timeouts de 5 minutos cobrem essa espera.

---

## 3. Verificar o cluster (evidências exigidas pelo enunciado)

```bash
kubectl -n inventory-manager get pods
kubectl -n inventory-manager get svc
kubectl -n inventory-manager get deployments
kubectl -n inventory-manager get statefulsets
kubectl -n inventory-manager get pvc
kubectl -n inventory-manager get configmap
kubectl -n inventory-manager get secret
```

Saída esperada: 3 pods Running (`frontend-...`, `backend-...`, `postgres-0`), 4 services, 2 deployments, 1 statefulset, 1 PVC `Bound`.

---

## 4. Acessar a aplicação

O Kind não expõe `NodePort` diretamente no host, então usamos `port-forward`. Abra **dois terminais separados** (port-forward bloqueia o terminal):

**Terminal A — frontend:**
```bash
kubectl -n inventory-manager port-forward svc/frontend-service 3000:3000
```

**Terminal B — backend:**
```bash
kubectl -n inventory-manager port-forward svc/backend-service 3001:3001
```

Em um terceiro terminal, valide o health do backend:
```bash
curl http://localhost:3001/api/ping
# resposta esperada: {"pong":true}
```

Abra no navegador: **http://localhost:3000**

### Credenciais (criadas pelo seed)

| Tipo | E-mail | Senha |
|---|---|---|
| Admin | `admin@inventory.com` | `123456` |
| Comum | `usuario@inventory.com` | `123456` |

---

## 5. Validar persistência (requisito do enunciado)

Demonstra que os dados sobrevivem ao reinício do banco.

```bash
# 1) Liste produtos antes
curl -s http://localhost:3001/api/products

# 2) (Opcional) Crie um produto pela UI em http://localhost:3000

# 3) Mate o pod do banco
kubectl -n inventory-manager delete pod postgres-0

# 4) Aguarde a recriação
kubectl -n inventory-manager get pods -w
# espere postgres-0 voltar a 1/1 Running, depois CTRL+C

# 5) Confirme que os dados continuam lá
curl -s http://localhost:3001/api/products
```

> Se o backend perder conexão com o banco enquanto ele reinicia, basta reiniciar o backend:
> ```bash
> kubectl -n inventory-manager rollout restart deployment/backend
> kubectl -n inventory-manager wait --for=condition=ready pod -l app=backend --timeout=120s
> ```

---

## 6. Verificar health checks

```bash
kubectl -n inventory-manager describe pod -l app=backend  | grep -A3 -E "Liveness|Readiness"
kubectl -n inventory-manager describe pod -l app=frontend | grep -A3 -E "Liveness|Readiness"
kubectl -n inventory-manager describe pod -l app=postgres | grep -A3 -E "Liveness|Readiness"
```

---

## 7. Logs (em caso de erro)

```bash
kubectl -n inventory-manager logs -l app=frontend --tail=100
kubectl -n inventory-manager logs -l app=backend  --tail=100
kubectl -n inventory-manager logs -l app=postgres --tail=100
```

---

## 8. Encerrar tudo

```bash
# parar port-forwards
pkill -f "kubectl.*port-forward"

# remover recursos do namespace (mantém o cluster)
kubectl delete namespace inventory-manager

# destruir cluster Kind
kind delete cluster --name inventory
```

---

## Recursos Kubernetes utilizados

| Recurso | Manifest | Finalidade |
|---|---|---|
| Namespace | `namespace.yaml` | Isolamento dos recursos |
| ConfigMap | `configmap.yaml` | Variáveis de ambiente não sensíveis |
| Secret | `secret.yaml` | Senha do banco e DATABASE_URL |
| PersistentVolumeClaim | `pvc.yaml` | Armazenamento persistente do Postgres |
| StatefulSet | `postgres-statefulset.yaml` | Banco PostgreSQL com PVC |
| Deployment (backend) | `backend-deployment.yaml` | API Express/Node |
| Deployment (frontend) | `frontend-deployment.yaml` | Next.js |
| Service ClusterIP | `postgres-statefulset.yaml` | Comunicação interna com o banco |
| Service Headless | `postgres-statefulset.yaml` | Requisito do StatefulSet |
| Service NodePort (30081) | `backend-deployment.yaml` | Acesso externo ao backend |
| Service NodePort (30080) | `frontend-deployment.yaml` | Acesso externo ao frontend |
| Job | `seed-db.yaml` | Popular o banco com dados iniciais |

Probes (`readinessProbe` + `livenessProbe`) configurados nos 3 workloads:
- **backend** → `GET /api/ping`
- **frontend** → `GET /login`
- **postgres** → `pg_isready`

---

## Imagens publicadas no registry (Docker Hub)

- [`lucaesposito2204/inventory-manager-frontend:latest`](https://hub.docker.com/r/lucaesposito2204/inventory-manager-frontend)
- [`lucaesposito2204/inventory-manager-backend:latest`](https://hub.docker.com/r/lucaesposito2204/inventory-manager-backend)
- `postgres:17-alpine` (imagem oficial do Docker Hub)
