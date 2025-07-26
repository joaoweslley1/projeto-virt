# Projeto de Virtualização

Equipe: João Weslley, Judá Valente

## Objetivo

*A descrever*

## Execução

Para executar a aplicação proposta é necessário seguir o seguinte passo a passo:

1. Garanta que o kind e o kubectl estejam instalados e funcionais, em seguida clone o respositório.

    ```bash
    git clone https://github.com/joaoweslley1/projeto-virt.git
    cd projeto-virt
    ```

2. É necessário criar um cluster kind capaz de executar o ingress-nginx, utilizando o `kind-config.yaml` disponibilizado com o comando:

    ```bash
    kind create cluster --config kind-config.yaml
    ```

3. Uma vez que o cluster for criado, é necessário executar um comando para instalar o ingress-nginx:

    ```bash
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.13.0/deploy/static/provider/kind/deploy.yaml

    # verifique o progresso da instalação. Ele estará pronto quando READY for "1/1" e o 
    # STATUS for "Running"
    kubectl get pod -n ingress-nginx
    ```

4. (Opcional) Caso as imagens estejam sendo executadas localmente, é necessário adicioná-las ao cluster kind usando os comandos a seguir:

    ```bash
    # kind load docker-image <nome-da-imagem:versao>
    kind load docker-image postgres:15 # exemplo com a imagem do postgres:15
    ```

5. Quando as imagens forem adicionadas é necessário subir cada um o secret, o confimap e os deployments na okubectl get pod -n ingress-nginxrdem a seguir:

    ```bash
    # inicia os namespaces
    kubectl apply -f namespaces.yaml

    # inicia o secret, pvc e statefulset + service do banco de dados
    kubectl apply -f database/secrets.yaml 
    kubectl apply -f database/pvc.yaml 
    kubectl apply -f database/statefulset.yaml

    # inicia o configmap, secrets e o deploy + service do backend
    kubectl apply -f backend/secrets.yaml 
    kubectl apply -f backend/configmap.yaml 
    kubectl apply -f backend/deployment.yaml

    # inicia o deploy + service do frontend 
    kubectl apply -f frontend/deployment.yaml

    # inicia o ingress
    kubectl apply -f ingress/ingress.yaml
    ```

6. Alterar o arquivo `/etc/hosts` no Linux para acessar a api e o frontend utilizando o localhost (127.0.0.1):

    ```bash
    127.0.0.1   app.local # acessar o frontend
    ```

## Endereço de acesso esperado

- Frontend: `app.local`
- Backend: `app.local/api`

## Arquitetura

```bash
.
├── backend
│   ├── secrets.yaml        # Informações secretas para o backend
│   ├── configmap.yaml
│   └── deployment.yaml
├── database
│   ├── statefulset.yaml
│   ├── pvc.yaml
│   └── secrets.yaml
├── frontend
│   └── deployment.yaml
├── ingress
│   └── ingress.yaml
├── kind-config.yaml        # Configuração do kind
├── namespace.yaml          
└── README.md
```
