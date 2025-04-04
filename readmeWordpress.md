# Infraestrutura AWS para WordPress: Escalabilidade e Alta Disponibilidade

## Descrição do Projeto

Este projeto implementa uma infraestrutura escalável e resiliente na Amazon Web Services (AWS) para efetuar o deploy de uma aplicação WordPress. A arquitetura consiste em duas instâncias EC2 localizadas em zonas diferentes, ambas conectadas a um banco de dados RDS e suportadas por um Load Balancer. Utilizando serviços como EC2, RDS, EFS, Auto Scaling e Load Balancer, a infraestrutura é projetada para suportar picos de tráfego, garantindo alta disponibilidade e desempenho para a aplicação WordPress.

## Objetivo

O objetivo deste projeto é criar uma arquitetura de nuvem que permita o deploy eficiente de uma aplicação WordPress, garantindo escalabilidade automática para lidar com variações na carga de trabalho. Além disso, o projeto visa proporcionar um ambiente seguro e eficiente para o armazenamento de dados e arquivos, utilizando os serviços da AWS.

## Arquitetura

A arquitetura do projeto é composta pelos seguintes componentes:

- **VPC (Virtual Private Cloud)**: Uma rede isolada onde todos os recursos da AWS serão criados.
- **Sub-redes**: Duas sub-redes públicas para as instâncias EC2 e duas sub-redes privadas para o banco de dados RDS.
- **EC2 (Elastic Compute Cloud)**: Duas instâncias que executam a aplicação web, localizadas em zonas diferentes para garantir alta disponibilidade.
- **RDS (Relational Database Service)**: Um banco de dados gerenciado que armazena os dados da aplicação, acessado por ambas as instâncias EC2.
- **EFS (Elastic File System)**: Um sistema de arquivos escalável que permite o compartilhamento de arquivos entre as instâncias EC2. 
- **É importante ressaltar que o EFS deve estar na mesma VPC e sub-rede que as instâncias EC2 para garantir a conectividade adequada.**
- **Auto Scaling**: Um grupo que ajusta automaticamente o número de instâncias EC2 com base na demanda.
- **Load Balancer**: Um balanceador de carga que distribui o tráfego entre as instâncias EC2, garantindo alta disponibilidade.

## Pré-requisitos

Antes de iniciar a implementação, certifique-se de que você possui:

- Uma conta na AWS com permissões adequadas para criar e gerenciar os recursos necessários para execução desta aplicação.
- Conhecimento básico em AWS.

## Tecnologias Utilizadas

- **Amazon Web Services (AWS)**: Plataforma de nuvem que fornece os serviços necessários para a implementação da infraestrutura.
- **EC2 (Elastic Compute Cloud)**: Para executar instâncias de servidores.
- **RDS (Relational Database Service)**: Para gerenciar o banco de dados.
- **EFS (Elastic File System)**: Para armazenamento de arquivos.
- **Auto Scaling**: Para escalabilidade automática.
- **Application Load Balancer**: Para balanceamento de carga.

## Funcionalidades

- **Escalabilidade Automática**: O Auto Scaling ajusta automaticamente o número de instâncias EC2 com base na carga de trabalho.
- **Balanceamento de Carga**: O Load Balancer distribui o tráfego entre as instâncias EC2, garantindo que nenhuma instância fique sobrecarregada.
- **Armazenamento Persistente**: O EFS fornece um sistema de arquivos compartilhado que pode ser acessado por várias instâncias EC2.
- **Banco de Dados Gerenciado**: O RDS oferece um banco de dados relacional gerenciado, facilitando a configuração, operação e escalabilidade.
- **Segurança**: A infraestrutura é configurada com grupos de segurança para controlar o acesso aos recursos.

# Instalação da Infraestrutura na AWS

## Parte 1: Configuração Inicial

### Etapa 1: Criação da VPC
1. Acesse a seção **VPC** em **Your VPCs**.
2. Clique em **Create VPC** e configure a VPC com 2 sub-redes públicas e 2 sub-redes privadas.

### Etapa 2: Criação de um Security Group
1. Navegue até a seção **EC2** em **Security Groups** e clique em **Create security group**.
2. Nas regras de entrada, edite as seguintes regras:
   - **HTTP (porta 80)** - Origem: `sg-0ea5c6c977dd921df`.
   - **SSH (porta 22)** - Origem: `0.0.0.0/0`.
   - **MYSQL/Aurora (porta 3306)** - Origem: `0.0.0.0/0`.
   - **NFS (porta 2049)** - Origem: `sg-0ea5c6c977dd921df`.
   - **TCP personalizado (porta 8080)** - Origem: `0.0.0.0/0`.
3. Nas regras de saída, edite as seguintes regras:
   - **HTTP (porta 80)** - Destino: `0.0.0.0/0`.
   - **HTTPS (porta 443)** - Destino: `0.0.0.0/0`.
   - **MYSQL/Aurora (porta 3306)** - Destino: `0.0.0.0/0`.
   - **NFS (porta 2049)** - Destino: `sg-0ea5c6c977dd921df`.
4. Finalize clicando em **Create security group**.

### Etapa 3: Criação de uma Instância RDS 
1. **Inicie o Processo de Criação**: 
   - Procure por "RDS" na barra de pesquisa e selecione **Aurora and RDS**. 
   - Clique em **Databases**.
   - Clique no botão **Create database**.

2. **Escolha o Tipo de Banco de Dados**:
   - Selecione o tipo de banco de dados que deseja usar.
   - Selecione "Standard Create".

3. **Configurações da Instância**:
   - Dê um nome à sua instância de banco de dados.
   - Insira o nome de usuário do administrador.
   - Em "Configurações de credenciais", selecione **Autogerenciada** e crie uma senha para o usuário administrador.

4. **Configurações de Banco de Dados**:
   - Escolha o tipo de instância que você quer utilizar.
   
5. **Configurações de Rede e Segurança**:
   - Escolha a VPC (Virtual Private Cloud) criada na Etapa 1.
   - Selecione o grupo de segurança criado na Etapa 2.

6. **Criar a Instância**:
   - Revise suas configurações e clique em **Create database** para iniciar a criação da instância.

7. **Aguarde a Criação**:
   - A criação da instância pode levar alguns minutos. 
   - Copie o endpoint da instância criada e cole no `script_Wordpress` fornecido neste repositório.

### Etapa 4: Criando um Sistema de Arquivos EFS
1. **Inicie o Processo de Criação**: 
   - Procure por "EFS" na barra de pesquisa e selecione **EFS**. Depois clique no botão **Create file system**.

2. **Configurações do Sistema de Arquivos**:
   - Dê um nome ao seu sistema de arquivos.
   - Escolha a VPC (Virtual Private Cloud) criada na Etapa 1.
   - Clique em Personalizar.
   - Em Tipo de sistema de arquivos, escolha entre Regional e OneZone.
   - Desmarque a caixa de seleção para desmarcar Ativar backups automáticos.
   - Em Gerenciamento do ciclo de vida, para Transição para acesso ocasional e para Transição para arquivamento, escolha Nenhum.
   - Para o modo de taxa de transferência, escolha Bursting.

3. **Configurações de Rede**:
   - Selecione a Zona de disponibilidade.
   - Selecione a sub-rede que o EFS estará disponível. 
   - Selecione o grupo de segurança criado na Etapa 2.

4. **Criar o Sistema de Arquivos**:
   - Revise suas configurações e clique em **Create file system** para iniciar a criação do sistema de arquivos.

5. **Aguarde a Criação**:
   - A criação do sistema de arquivos pode levar alguns minutos. 
   - Copie o ID do sistema de arquivos e cole no `script_Wordpress` fornecido neste repositório.

### Etapa 5: Criação de uma Instância EC2
1. Navegue até a seção **EC2** em **Instances** e clique em **Launch instances**.
2. Escolha a AMI **Amazon Linux 2023 AMI**.
3. Associe a instância à VPC criada anteriormente, colocando-a em uma sub-rede pública.
4. Crie e vincule uma chave `.pem` à instância para permitir o acesso SSH.
5. Associe a instância ao Security Group criado anteriormente.
6. Em **Detalhes avançados**, no campo **Dados do usuário (opcional)**, cole o arquivo: `script_Wordpress` fornecido neste repositório.
7. Finalize a criação da instância clicando em **Launch instance**.

### Etapa 6: Acesso à Instância EC2 via SSH
A conexão pode ser realizada utilizando o Visual Studio Code.

1. Na plataforma da AWS, selecione a instância criada anteriormente e clique em **Connect**.
2. Copie o comando exibido no campo **SSH Client**.
3. No terminal do Visual Studio Code, navegue até a pasta onde está armazenada a chave `.pem` criada  na Etapa 5.
4. Cole o comando que você acabou de copiar na AWS, no campo **SSH Client**.
5. Certifique-se de estar logado previamente na plataforma da AWS com suas credenciais de acesso (via terminal do Visual Studio Code).

### Testes
Nesta seção, apresentamos algumas capturas de tela que demonstram o funcionamento do projeto:

- Captura de tela da Página no Navegador **ec2_1**
- Captura de tela da Página no Navegador **ec2_2**
- Captura de tela com acesso via cliente MySQL da **ec2_1**
- Captura de tela com acesso via cliente MySQL da **ec2_2**
- Colocar um print em um local apropriado(arquitetura) do proposito do projeto

## Parte 2: Configuração de Auto Scaling e Load Balancer na AWS

### Etapa 1: Criação da imagem.
1. **Acesse o Console da AWS**:
   - Faça login no AWS Management Console e navegue até o serviço **EC2**.

2. **Selecione a Instância**:
   - No painel de navegação à esquerda, clique em **Instâncias**.
   - Localize e selecione a instância da qual você deseja criar a imagem.

3. **Crie a Imagem**:
   - Navegue até a seção **EC2**, selecione a instância, clique com o botão **Ações**.
   - Selecione **Imagem e Modelos** depois clique em **Criar Imagem**.

4. **Configure os Detalhes da Imagem**:
   - Dê um nome para a imagem.
   - Adicione uma descrição opcional para a imagem.
   - Selecione para reiniciar a instância durante a criação da imagem.
   
5. **Crie a Imagem**:
   - Clique em **Criar Imagem**.

### Etapa 2: Criação de um Launch Template
1. **Inicie o Processo de Criação**: 
   - Navegue até a seção **EC2** em **Launch instances** e clique em **Create Launch instances**.
   - Dê um nome ao seu template.
   - Descreva a versão do template.
   - Em **My AMIs** selecione **De minha propriedade** e escolha a Imagem criada na Etapa 1.
   - Escolha o tipo de instância.
   - Selecione a chave `.pem` criada a chave `.pem` criada  na Etapa 5 - (da Parte 1).
   - Escolha a VPC (Virtual Private Cloud) criada na Etapa 1 - (da Parte 1).
   - Selecione o grupo de segurança criado na Etapa 2 - (da Parte 1).
   - Clique em **Create launch template**.

### Etapa 3: Criação de um Auto Scaling Group
1. Navegue até a seção **EC2** em **Auto Scaling Groups** e clique em **Create Auto Scaling group**.
   - Dê um nome ao seu Auto Scaling Group.
   - Selecione o Launch Template que você criou anteriormente.
   - Clique em **Next**.

2. **Configurações de Rede**:
   - Selecione a VPC e as sub-redes onde o Auto Scaling Group deve ser criado.
   - Clique em **Next**.

3. **Configurações de Escalonamento**:
   - Defina a Capacidade Desejada, Capacidade mínima desejada e Capacidade máxima desejada de instâncias.
   - Selecione **Política de dimensionamento com monitoramento do objetivo**, dê um nome à política de escalabilidade e escolha a média de utilização da CPU.
   - Clique em **Next**.

4. **Revisão e Criação**:
   - Revise suas configurações e clique em **Create Auto Scaling group**.

### Etapa 4: Associar o Load Balancer ao Auto Scaling Group
1. **Acesse o Auto Scaling Group**:
   - Navegue até o Auto Scaling Group que você criou anteriormente.
   - Escolha o tipo de Load Balancer.
   - Dê um nome ao seu Load Balancer.
   - Selecione o esquema do balanceador de carga.

2. **Configurações de Rede**:
   - Escolha a VPC (Virtual Private Cloud) criada na Etapa 1.

3. **Configurações de Segurança**:
   - Selecione o grupo de segurança.

4. **Revisão e Aplicação**:
   - Revise suas configurações e clique em **Update**.

### Etapa 5: Criação de um Health Check
1. Navegue até a seção **EC2** em **Target Groups** e clique no Target Group.

2. **Configurações de Verificação de Saúde**:
   - Clique na aba **Health checks**.
   - Insira o caminho que o Load Balancer deve verificar.
   - Selecione **HTTP**.
   - Insira **traffic port**.
   - Defina o número de verificações bem-sucedidas necessárias para considerar a instância saudável (por exemplo, 2).
   - Defina o número de verificações malsucedidas necessárias para considerar a instância não saudável (por exemplo, 2).
   - Defina o tempo limite para a verificação de saúde (por exemplo, 5 segundos).
   - Defina o intervalo entre as verificações de saúde (por exemplo, 30 segundos).
   - Clique em **Save changes**.

### Etapa 6: Acesso à Instância EC2 via SSH
Por meio dessa conexão remota, pode ser verificado os registros de erros em: `/var/log/nginx_monitor.log`. A conexão pode ser realizada utilizando o Visual Studio Code.

1. Na plataforma da AWS, selecione a instância criada anteriormente e clique em **Connect**.
2. Copie o comando exibido no campo **SSH Client**.
3. No terminal do Visual Studio Code, navegue até a pasta onde está armazenada a chave `.pem` criada anteriormente.
4. Cole o comando que você acabou de copiar na AWS, no campo **SSH Client**.
5. Certifique-se de estar logado previamente na plataforma da AWS com suas credenciais de acesso (via terminal do Visual Studio Code).

### Testes
Nesta seção, apresentamos algumas capturas de tela que demonstram o funcionamento do projeto:

- Captura de tela auto scaling funcionado
- Captura de tela load balance funcionando
- Captura de tela health ckeck funcionando

## Conclusão

Neste projeto, implementamos uma infraestrutura escalável e resiliente na Amazon Web Services (AWS) para hospedar uma aplicação WordPress. Através da configuração de instâncias EC2, um banco de dados RDS, um sistema de arquivos EFS, Auto Scaling e Load Balancer, criamos um ambiente robusto que não apenas suporta picos de tráfego, mas também assegura alta disponibilidade e desempenho consistente.

A utilização de serviços gerenciados da AWS simplificou significativamente a configuração e a manutenção da infraestrutura, permitindo que a equipe se concentrasse no desenvolvimento da aplicação, em vez de se preocupar com a gestão de servidores. Além disso, a implementação de práticas de segurança, como grupos de segurança e controle de acesso, garantiu a proteção dos dados e recursos.

Com a configuração de Auto Scaling, a aplicação se adapta automaticamente às variações na carga de trabalho, assegurando que os usuários tenham sempre uma experiência fluida e responsiva. O Load Balancer distribui o tráfego de forma eficiente entre as instâncias, evitando sobrecargas e aumentando a resiliência do sistema.

Em suma, este projeto ilustra a eficácia da AWS em fornecer soluções de infraestrutura que atendem às exigências de aplicações modernas, escaláveis e seguras. A continuidade do monitoramento e a realização de testes regulares são fundamentais para garantir que a infraestrutura permaneça otimizada e preparada para atender às demandas futuras.
