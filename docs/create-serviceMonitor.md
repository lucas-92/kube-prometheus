## O que é um Service Monitor?
Um Service Monitor é um recurso no Prometheus Operator que define como os serviços em um cluster Kubernetes devem ser monitorados. Ele é usado para especificar quais endpoints o Prometheus deve rastrear para coletar métricas de serviços e aplicações que estão rodando no cluster.

No contexto de Kubernetes, o Prometheus Operator é responsável por automatizar a configuração do Prometheus em um ambiente Kubernetes, facilitando o processo de descobrir e monitorar aplicações rodando no cluster. O Service Monitor faz parte dessa automação e fornece uma maneira de Prometheus descobrir automaticamente os serviços que ele deve monitorar.
Como funciona o Service Monitor?

O Service Monitor atua como um intermediário entre o Prometheus e os serviços que expõem métricas no Kubernetes. Ele utiliza selectors para identificar serviços que devem ser monitorados e define parâmetros como as portas e os caminhos de onde Prometheus deve coletar as métricas.
Estrutura básica de um Service Monitor:

### Exemplo de servicemonitor.yaml:
```
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: meu-servico-monitor
  labels:
    release: prometheus
spec:
  selector:
    matchLabels:
      app: minha-aplicacao
  endpoints:
  - port: metrics
    interval: 30s
    path: /metrics
```

### Componentes principais:
```
    metadata.name: Nome do Service Monitor.
    spec.selector.matchLabels: Especifica as labels que serão usadas para identificar os serviços que o Prometheus deve monitorar. Neste exemplo, o Service Monitor busca por serviços que tenham o rótulo app: minha-aplicacao.
    spec.endpoints: Define detalhes sobre os endpoints de onde as métricas serão coletadas, como:
        port: Porta do serviço onde as métricas estão expostas.
        path: Caminho onde o Prometheus vai acessar as métricas (normalmente /metrics).
        interval: Frequência com que as métricas serão coletadas.
```
### Exemplo de uso
- Descoberta de Serviços: Prometheus usa o Service Monitor para descobrir automaticamente serviços com métricas expostas. Isso elimina a necessidade de configurar manualmente cada serviço no Prometheus.
- Monitoramento Centralizado: Você pode ter vários Service Monitors para monitorar diferentes tipos de serviços, o que centraliza o monitoramento sem precisar editar diretamente as configurações do Prometheus.

### Por que usar Service Monitors?
- Automatização: O Service Monitor simplifica a configuração, já que novos serviços que seguem o padrão de rotulagem definido podem ser automaticamente descobertos pelo Prometheus.
- Escalabilidade: Em ambientes dinâmicos, como Kubernetes, onde novos serviços podem ser criados frequentemente, o Service Monitor evita a necessidade de manter configurações de monitoramento manualmente.
- Integração nativa com Kubernetes: Ele utiliza o sistema de rótulos e selectors do Kubernetes, integrando-se perfeitamente ao ecossistema.

### Resumo
- O Service Monitor é uma forma eficiente de definir quais serviços e aplicações o Prometheus deve monitorar em um cluster Kubernetes, fornecendo flexibilidade e escalabilidade, especialmente em ambientes com muitas aplicações dinâmicas.
