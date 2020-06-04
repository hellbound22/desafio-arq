# Solução do desafio SA 
##### Rodrigo de Oliveira Mauricio

Infelizmente não tive tempo para produzir nenhum código, mas fiquem a vontade para olhar meu GitHub :)

## 1- Arquitetura
A minha solução utiliza uma arquitetura de micro-serviços simples:

![imagem](/arq.png)

### 1.1- Comunicação 
Mesmo somente a API gateway extando exposta à internet, a comunicação entre os micro-serviços precisa estar segura. Utilizar SSL adciona alguns ms de processamento mas previne sniffing de pacotes mesmo que um agente malicioso entre na rede, então é uma troca que vale a pena. Em um mundo ideal os certificados seriam emitidos por uma CA.

Uma outra opção, dependendo das reais necessidades do sistema, seria utilizar ZeroMQ. Isso faria com que os serviços se comunicassem extremamente mais rápido, com performances próximas a compartilhamento direto de memória.

## 2- Protocolo
Toda a comunicação entre os serviços usa uma especificação escrita em ProtoBuf. Isso nos permite ter um código que pode ser fácilmente modificado e expandido com outras informaçoes, serviços, tipos de dados, sem a necessidade de mecher diretamente em um código que já é estável. 

Obviamente, se haver necessidade, a utilização de JSON ou XML (muito usado pelo governo), pode ser implantanda como um side-car para os serviços caso sua necessidade não tenha sido aparente no começo do desenvolvimento do sistema.

## 3- Banco de dados
Como especificado na proposta do desafio, precisamos de 3 banco de dados diferentes, com diferentes requisitos e propósitos. 
Para manter disponibilidade e segurança, pode-se utilizar o Amazon RDS de um BD master. Assim podemos replicar os containers sob-demanda e escalar nosso sistema sem se preocupar com tráfego sobrecarregando um único BD.

### 3.1- Relações
Assumindo que os bancos não se conectam entre si diretamente, é preciso utilizar um sistema de endereços(id) unificado e que possa ser calculado sem necessidade de conhecimento prévio. Na minha experiência, um bom sistema é o que é "padrão" ao utilizar a Hyperledger Sawtooth, onde o endereço é dado por uma hash, e você tem prefixos de 4 bits, no caso uma estrutura de endereço podia ser escrita assim: 
```
[prefixo de versão(para manter retrocompatibilidade de dados se necessário)] + [prefixo de tipo (pessoa física, juridica, imóvel, financeiros...)] + hash calculado
```

Isso permite interoperabilidade e, se implementado corretamente, pode significar boas melhoras de performance.

## 4- Tecnologias 
- PostgreSQL
- Redis
- Python
	- Flask
	- OpenSSL
	- protobuf
	- Rust (Como linguagem auxiliar para desenvolvimento de bibliotecas de alta performance e alto tráfego)
- Docker (Conteriners/Micro-serviços)
