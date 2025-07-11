Para instalar o Prometheus usando Helm, siga os passos abaixo. O Helm simplifica muito a implantação de aplicações Kubernetes, e o Prometheus é frequentemente implantado dessa forma.

-----

### Pré-requisitos

Antes de começar, certifique-se de que você tem:

  * **Kubernetes Cluster:** Um cluster Kubernetes em execução e acessível.
  * **kubectl:** A ferramenta de linha de comando `kubectl` configurada para se comunicar com seu cluster.
  * **Helm:** O gerenciador de pacotes Helm instalado em sua máquina local. Se você não o tem, pode instalá-lo seguindo as instruções em [helm.sh/docs/intro/install/](https://helm.sh/docs/intro/install/).

-----

### Passos para Instalar o Prometheus com Helm

#### 1\. Adicionar o Repositório do Prometheus Community Helm

Primeiro, você precisa adicionar o repositório oficial do Prometheus Community Helm ao seu Helm:

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

  * `helm repo add prometheus-community ...`: Adiciona o repositório que contém os charts do Prometheus.
  * `helm repo update`: Atualiza seus repositórios Helm para garantir que você tenha as últimas versões dos charts.

-----

#### 2\. Criar um Namespace (Opcional, mas Recomendado)

É uma boa prática instalar o Prometheus em seu próprio namespace para melhor organização. Você pode criar um usando `kubectl`:

```bash
kubectl create namespace prometheus
```

-----

#### 3\. Instalar o Prometheus usando Helm

Agora você pode instalar o Prometheus. O chart `kube-prometheus-stack` é a maneira mais comum e abrangente de instalar o Prometheus, Grafana e o Alertmanager, juntamente com exporters essenciais.

**Instalação Básica:**

```bash
helm install prometheus prometheus-community/kube-prometheus-stack --namespace prometheus
```

  * `helm install prometheus`: Inicia a instalação e dá o nome `prometheus` à sua release do Helm.
  * `prometheus-community/kube-prometheus-stack`: Especifica o chart que você deseja instalar do repositório `prometheus-community`.
  * `--namespace prometheus`: Instala a release no namespace `prometheus` que você criou (ou será criado se não existir).

**Instalação Personalizada (Recomendado para Produção):**

Para uma instalação mais robusta e personalizável, é altamente recomendável criar um arquivo de valores (`values.yaml`) para configurar o Prometheus e seus componentes.

1.  **Gerar um arquivo de valores padrão:**

    ```bash
    helm show values prometheus-community/kube-prometheus-stack > prometheus_values.yaml
    ```

    Isso salvará a configuração padrão do chart em um arquivo chamado `prometheus_values.yaml`.

2.  **Editar o arquivo `prometheus_values.yaml`:**

    Abra este arquivo em seu editor de texto preferido. Aqui estão algumas das configurações comuns que você pode querer ajustar:

      * **`grafana.enabled`**: `true` ou `false` para habilitar/desabilitar o Grafana.
      * **`grafana.adminPassword`**: Defina uma senha forte para o usuário `admin` do Grafana.
      * **`prometheus.prometheusSpec.storageSpec.volumeClaimTemplate.spec.resources.requests.storage`**: Ajuste o tamanho do armazenamento persistente para o Prometheus (ex: `50Gi`).
      * **`alertmanager.enabled`**: `true` ou `false` para habilitar/desabilitar o Alertmanager.
      * **`defaultRules.create`**: `true` para criar um conjunto padrão de regras de alerta.
      * **`nodeExporter.enabled`**: `true` para coletar métricas dos nós do cluster.

    Exemplo de modificação em `prometheus_values.yaml`:

    ```yaml
    # ... (outras configurações) ...

    grafana:
      enabled: true
      adminPassword: "sua_senha_segura" # Altere esta linha!

    prometheus:
      prometheusSpec:
        storageSpec:
          volumeClaimTemplate:
            spec:
              resources:
                requests:
                  storage: 50Gi # Exemplo: 50 GB de armazenamento

    # ... (outras configurações) ...
    ```

3.  **Instalar com o arquivo de valores personalizado:**

    ```bash
    helm install prometheus prometheus-community/kube-prometheus-stack --namespace prometheus -f prometheus_values.yaml
    ```

    O `-f prometheus_values.yaml` instrui o Helm a usar suas configurações personalizadas.

-----

#### 4\. Verificar o Status da Instalação

Após a instalação, você pode verificar o status dos pods no namespace `prometheus`:

```bash
kubectl get pods -n prometheus
```

Você deve ver pods para Prometheus, Grafana, Alertmanager, e vários exporters em estado `Running`.

-----

#### 5\. Acessar a Interface do Prometheus e Grafana

**Acessar a Interface do Prometheus:**

O serviço do Prometheus geralmente é do tipo `ClusterIP`. Para acessá-lo, você pode usar o `kubectl port-forward`:

```bash
kubectl port-forward svc/prometheus-kube-prometheus-prometheus 9090:9090 -n prometheus
```

Agora, abra seu navegador e acesse `http://localhost:9090`.

**Acessar a Interface do Grafana:**

Similarmente, para o Grafana:

```bash
kubectl port-forward svc/prometheus-grafana 3000:80 -n prometheus
```

Abra seu navegador e acesse `http://localhost:3000`. Use `admin` como nome de usuário e a senha que você configurou em seu `prometheus_values.yaml` (ou a senha padrão se não a alterou, que pode ser `prom-operator` para versões mais antigas do chart, mas é altamente recomendável alterá-la).

-----

### Desinstalar o Prometheus

Se você precisar desinstalar o Prometheus, use o seguinte comando:

```bash
helm uninstall prometheus -n prometheus
```

Isso removerá todos os recursos criados pela release do Helm `prometheus` no namespace `prometheus`.

-----

Com esses passos, você terá o Prometheus, Grafana e Alertmanager funcionando em seu cluster Kubernetes, prontos para monitorar suas aplicações e infraestrutura.