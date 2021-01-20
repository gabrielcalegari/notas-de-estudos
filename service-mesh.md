# Service Mesh

## O que é?

Service Mesh é uma rede de microsserviços que oferece, de forma consistente: segurança, service discovery, application tracing, monitoring e tolerância a falhas, sem a necessidade de um recurso compartilhado como, por exemplo, um API Gateway.

Sua principal função é abstrair para os desenvolvedores uma camada de infraestrutura para comunicação entre serviços que estão na mesma rede, o que é comumente chamado de tráfego leste-oeste.

## Componentes

Um service mesh tem dois componentes principais:

- Control Plan: O plano de controle mantém o estado do sistema e desempenha um papel de coordenação. Ele fornece um registro centralizado de onde os serviços estão sendo executados e as políticas que restringem o tráfego. Ele deve ser dimensionado para lidar com dezenas de milhares de instâncias de serviço e, em tempo real, atualizar o plano de dados de maneira eficiente.
- Data Plan: o plano de dados é distribuído e responsável pela transmissão de dados entre diferentes serviços. Deve ser de alto desempenho e integrar-se ao plano de controle.

## Service mesh mais utilizados

### Istio

