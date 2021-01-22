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

- _productpage_: esse microsserviço chama os microsserviços _details_ e _reviews_ para popular a página.
- _details_: contém informações sobre livros.
- _reviews_: contém avaliações de livros. Ele também chama o microsserviço _ratings_.
- _ratings_: contém informações de ranking que acompanham uma avaliação de livro.

O microsserviço _reviews_, escrito em Java, possui três versões diferentes:

- Versão v1 não chama o microsserviço _ratings_.
- Versão v2 chama o microsserviço _ratings_, e mostra cada avaliação como estrelas pretas de 1 a 5.
- Versão v3 chama o microsserviço _ratings_, e mostra cada avaliação como estrelas vermelhas de 1 a 5.

![Exemplo de arquitetura de microsserviços usando Istio](https://github.com/gabrielcalegari/notas-de-estudos/blob/main/istio-architecture.png)

2. Em cada um dos microsserviços é injetado pelo Istio um proxy chamado de Envoy. O Envoy é implantado como sidecar, isto é, na mesma POD. O Envoy é responsável por todo tráfego de entrada e saída daquela POD. Um Ingress Envoy também pode ser utilizado como um gateway de entrada do Istio para acessar seus serviços por meio de um endereço IP público.

3. Istio Pilot é um componente do control plane responsável pelo ciclo de vida das instâncias do Envoy implantadas na malha de serviço do Istio. Ele expõe APIs para service discovery, atualizações dinâmicas para pools de balanceamento de carga e tabelas de roteamento. Essas APIs separam o Envoy das nuances específicas da plataforma, simplificando o design e aumentando a portabilidade entre as plataformas. O Pilot é usado aqui para gerenciar o roteamento entre as versões do serviço _reviews_. Na imagem, o usuário configurou para que as versões 1 e 3 do microsserviço recebam 50% do tráfego; a versão 2 é ativada apenas para um usuário específico.

4. Istio Mixer é outro componente do control plane. O Mixer fornece uma camada de intermediação genérica entre o código do aplicativo e os back-ends de infraestrutura. Seu design move as decisões de política da camada do aplicativo para a configuração, sob o controle do operador. Em vez de ter o código do aplicativo integrado com back-ends específicos, o código do aplicativo faz uma integração bastante simples com o Mixer, e o Mixer assume a responsabilidade pela interface com os sistemas de back-end. No sistema em questão, o usuário pode configurar, utilizando o Mixer, o controle de acesso para serviços para negar o tráfego da versão 3 do microsserviço _review_ a qualquer acesso ao microsserviço de _ratings_. O Mixer também pode ser utilizado para regras de telemetria para encaminhamento a addons como Grafana e Prometheus e coleta de traces com Jeager.

5. O objetivo do Istio Auth é aprimorar a segurança dos microsserviços e sua comunicação sem exigir alterações no código do serviço. É responsável por: (1) 
fornecer a cada serviço uma identidade forte que representa sua função para permitir a interoperabilidade entre clusters e nuvens; (2) proteger serviço para comunicação de serviço; (3) fornecer um sistema de gerenciamento de chaves para automatizar a geração, distribuição, rotação e revogação de chaves e certificados.

6. A arquitetura passa a incluir um backing service externo: uma base de dados MySql. 

7. Três microserviços — _details_, _ratings_ e _reviews_ — são modificados para usar o banco de dados MySQL.

8. Por padrão, as aplicações ativas via Istio são incapazes de acessar uma URL de fora do cluster. Todo o tráfego de saída no pod é redirecionado por seu proxy Envoy, que lida apenas com destinos dentro do cluster. No entanto, o Istio permite que você defina um ServiceEntry para controlar a saída para serviços externos. 
