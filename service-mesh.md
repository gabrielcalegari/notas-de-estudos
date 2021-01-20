# Service Mesh

## O que é?

Service Mesh é uma rede de microsserviços que oferece, de forma consistente: segurança, service discovery, application tracing, monitoring e tolerância a falhas, sem a necessidade de um recurso compartilhado como, por exemplo, um API Gateway.

Sua principal função é abstrair para os desenvolvedores uma camada de infraestrutura para comunicação entre serviços que estão na mesma rede, o que é comumente chamado de tráfego leste-oeste.

## Componentes

Um service mesh tem dois componentes principais:

- Control Plane: O plano de controle mantém o estado do sistema e desempenha um papel de coordenação. Ele fornece um registro centralizado de onde os serviços estão sendo executados e as políticas que restringem o tráfego. Ele deve ser dimensionado para lidar com dezenas de milhares de instâncias de serviço e, em tempo real, atualizar o plano de dados de maneira eficiente.
- Data Plane: o plano de dados é distribuído e responsável pela transmissão de dados entre diferentes serviços. Deve ser de alto desempenho e integrar-se ao plano de controle.

## Service mesh mais utilizados

### Istio

#### Exemplo

1. O usuário implanta seu aplicativo no Kubernetes. O aplicativo, “BookInfo”, é composto por quatro microsserviços, cada um escrito em uma linguagem diferente: Python, Java, Ruby e Node.js. 

- productpage: esse microsserviço chama os microsserviços _details_ e _reviews_ para popular a página.
- details: contém informações sobre livros.
- reviews: contém avaliações de livros. Ele também chama o microsserviço _ratings_.
- ratings: contém informações de ranking que acompanham uma avaliação de livro.

O microsserviço Reviews, escrito em Java, possui três versões diferentes:

- Versão v1 não chama o microsserviço _ratings_.
- Versão v2 chama o microsserviço _ratings_, e mostra cada avaliação como estrelas pretas de 1 a 5.
- Versão v3 chama o microsserviço _ratings_, e mostra cada avaliação como estrelas vermelhas de 1 a 5.

![Exemplo de arquitetura de microsserviços usando Istio](https://github.com/gabrielcalegari/notas-de-estudos/blob/main/istio-architecture.png)

2. Em cada um dos microsserviços é injetado pelo Istio um proxy chamado de Envoy. O Envoy é implantado como sidecar, isto é, na mesma POD. O Envoy é responsável por todo tráfego de entrada e saída daquela POD. Um Ingress Envoy também pode ser utilizado como um gateway de entrada do Istio para acessar seus serviços por meio de um endereço IP público.

3. Istio Pilot é um componente do control plane responsável pelo ciclo de vida das instâncias do Envoy implantadas na malha de serviço do Istio. Ele expõe APIs para service discovery, atualizações dinâmicas para pools de balanceamento de carga e tabelas de roteamento. Essas APIs separam o Envoy das nuances específicas da plataforma, simplificando o design e aumentando a portabilidade entre as plataformas.
