# Loansink

Esta aplicação gera solicitações de empréstimo a cada poucos segundos e as 
coloca como mensagens na fila configurada em `application.properties`.

O sistema de mensagens de destino é determinado pelo Spring Cloud Stream com 
base no mecanismo de autoconfiguração para o Spring Boot.

Portanto, se `spring-kafka` estiver no classpath, será utilizado 
o sistema de mensagens do Kafka.

```xml
<dependency>
	<groupId>org.springframework.kafka</groupId>
	<artifactId>spring-kafka</artifactId>
</dependency>
``` 