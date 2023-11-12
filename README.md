## Problema

Você simplesmente deseja escrever lógica para sua aplicação orientada a eventos, mas o código de mensagens básicas continua atrapalhando e está consumindo seu tempo. Conectar suas aplicações a servidores de mensagens é complicado, e você precisa lidar com várias tecnologias de mensagens em sua organização, dependendo da equipe com a qual está trabalhando.

## Solução

O Spring Cloud Stream cuida do complicado código de inicialização para você, permitindo que você crie uma lógica de negócios limpa e fácil de manter. O Spring Cloud Stream unifica perfeitamente muitos protocolos de mensagens diferentes por trás de uma API fácil de usar, eliminando quaisquer diferenças sutis na abordagem ou recursos (como particionamento ou trocas, por exemplo), para que você possa se concentrar em construir soluções orientadas a eventos que "simplesmente funcionam".

## Pré-requisitos

Essas aplicações orientadas a eventos são construídas com: [Spring Boot][boot], [Spring Cloud Stream][stream], [Maven][maven], e [Java 8][java].

Já o server-side é executado em [Docker][docker] e inclui: [Kafka][kafka], [Zookeeper][zookeeper], [RabbitMQ][rabbit], and [KafDrop][kafdrop] (imagem by Obsidian Dynamics).

[stream-docs]: https://docs.spring.io/spring-cloud-stream/docs/current/reference/htmlsingle/
[boot]: https://spring.io/projects/spring-boot
[stream]: https://spring.io/projects/spring-cloud-stream
[maven]: https://maven.apache.org/
[java]: https://adoptopenjdk.net/
[docker]: https://www.docker.com/
[kafka]: https://kafka.apache.org/
[zookeeper]: https://zookeeper.apache.org/
[rabbit]: https://www.rabbitmq.com/
[kafdrop]: https://hub.docker.com/r/obsidiandynamics/kafdrop


## Explicação dos Micros-serviços

### LoanSource

O Loansource micro-serviço (na pasta /loansource). Este microsserviço atua como fonte de mensagens de eventos, esses eventos são Loan aplicações semelhantes às que você veria no mundo bancário e financeiro. Cada empréstimo tem um “nome”, um “valor” e um “status” (que é definido como PENDING inicialmente).

### LoanCheck

O Loancheck micro-serviço (na pasta /loancheck). Este microsserviço atua como um Loan processador, ele verifica quais empréstimos são bons para fazer e os classifica em APPROVED ou DECLINED como estados.

## Etapas para execução

### Etapa 1: Iniciar os servidores de mensagens

Em uma nova janela do terminal, vá para a pasta raiz do projeto e emita o seguinte comando( É necessário ter o Docker instalado ):

```sh
   ./start-servers.sh
```

Este script iniciará o Kafka e o RabbitMQ e transmitirá a saída do log de ambos para a janela do terminal (a menos que você saia com Ctrl-C).

### Etapa 2: Gerar alguns eventos de empréstimo

Em uma nova janela de terminal, vá para o diretório "/loansource" usando "cd", em seguida, utilize o seguinte comando:

```sh
   ./mvnw clean package spring-boot:run -DskipTests=true -Pkafka
```

Assim que o loansource for iniciado, na janela do terminal, você verá uma mensagem a cada 10 segundos informando que um novo evento de empréstimo foi postado na plataforma de mensagens do PENDING estado. Deixe este microsserviço em execução e passe para a próxima etapa.

### Etapa 3: Processar os eventos do empréstimo

Em uma nova janela de terminal, vá para o diretório "/loancheck" usando "cd", em seguida, utilize o seguinte comando:

```sh
   ./mvnw clean package spring-boot:run -DskipTests=true -Pkafka
```

Assim que o loancheck for iniciado, na janela do terminal, você deverá ver uma mensagem a cada 10 segundos informando que um novo PENDING aplicativo de empréstimo foi lido na plataforma de mensagens e em seguida terá como estado APPROVED ou DECLINED.

### Etapa 4: Verificar as mensagens no Kafka

Você pode acessar o Kafka para observar as mensagens que foram processadas no seguinte Link: http://localhost:9000/

Procure pelo Título "Topics", Clice no nome da aplicação(output) e logo em seguida clique em "View Messages".

### Etapa 5: Interromper a demonstração

Depois de concluir os micros-serviços, em cada uma das janelas do terminal do /loansourcee dos /loancheck micros-serviços, pressione Ctrl-C. O aplicativo será interrompido e o processamento do evento será interrompido.

Volte para o terminal que você utilizou o comando "./start-servers.sh", para interromper tudo, execute o seguinte comando:

```sh
   ./stop-servers.sh
```