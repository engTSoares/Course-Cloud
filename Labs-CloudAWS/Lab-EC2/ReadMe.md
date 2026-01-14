# Introdução ao Amazon EC2 

### Sobre 📃
Esse é um laboratório da AWS com a finalidade de aplicação e fixação de conhecimentos acerca do serviço AWS Elastic Compute Cloud (EC2).<br> ⚠️ Todas as informações expostas aqui são disponibilizadas pela própria AWS de maneira gratuita. (disponível em: [Guia EC2](https://docs.aws.amazon.com/pt_br/AWSEC2/latest/UserGuide/concepts.html))

***
### Objetivos do laboratório 🎯

I. Iniciar um servidor web com proteção de encerramento ativada<br>
II. Monitoramento da instância <br>
III. Aplicar permissão para acesso HTTP por meio da modificação do grupo de segurança do servidor <br>
IV. Redimensionar a instância do Amazon EC2 de acordo com a necessidade <br>
V. Testar a proteção de encerramento e terminar a instância do EC2
***
<br>

# Atividade 1 -Iniciar a instância EC2  

1) Para a realização desse laboratório utilizei o [Controle de Gerenciamento da AWS](https://aws.amazon.com/pt/console/). É através dessa interface web que irei acessar os serviços da AWS, como o EC2,  e realizar todo o gerenciamento e configuração. 
<br>
<p align="center">
  <img src="imgs/AWS-console-de-gerenciamento.jpg" width="500" />
</p>
<p align="center">
Fonte: AWS News Blog
</p>
</br>

2) Dentro do console de gerenciamento,no menu de **Serviços**, selecione EC2 e vá até o **Painel do EC2** à esquerda.

## Etapa 1 - Executando e nomeando uma instância
3) No Painel do EC2, selecione `Instância` >  `Executar instância` 

4) Dê um nome à sua instância localizado no painel **Nome e tags**

## Etapa 2 - Selecionando uma imagem de máquina (AMI)

5) Em **Imagem de máquina da Amazon (AMI)** selecione a imagem do Amazon Linux 2023* (Amazon Linux 2023 kernel -6.12 AMI),  caso já esteja selecionada, prossiga normalmente.

## Etapa 3 - Selecionando o tipo da instância

6) Na aba de **Tipo de instância** escolha a `t3.micro`

## Etapa 4 - Configurando par de chaves

7) Nesse laboratório não será necessário o login na instância, e por isso, iremos selecionar `Proceder sem um par de chaves`, o que não é recomendado. 

## Etapa 5 - Definição das configurações de rede

8) Primeiro vamos clicar no botão `Editar`
9) Selecionei uma VPC já criada para a realização desse laboratório (`Lab VPC`). Mais à frente irei realizar um laboratório realizando o passo a passo como ocorre essa criação.
10) Na mesma aba de **Configuração de rede** iremos selecionar a opção `criar grupo de segurança`, e após isso definiremos o nome e a descrição. 
11) Para auxiliar o reforço da segurança da instância, removeremos com o botão `Remover` as regras do grupo de segurança de entrada, pois não iremos fazer conexão com a instância

## Etapa 6 - Adição de armazenamento

12) No painel de **Configurar armazenamento**, irei usar 8 GiB, gp3. 

## Etapa 7 - Proteção de encerramento e dados de usuário

13) Para ativar a proteção contra encerramento indesejado da instância, vá em **Proteção de encerramento** no painel de **Detalhes Avançados** e selecione `habilitar`

14) No final dessa mesma aba de **Detalhes Avançados**, cole o seguinte script:
```
#!/bin/bash

# Install Apache Web Server and PHP
dnf install -y httpd php

# Install MySQL client if needed (opcional)
dnf install -y mariadb105

# Download Lab files
wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-100-RESTRT-1/267-lab-NF-build-vpc-web-server/s3/lab-app.zip

# Unzip requires unzip package
dnf install -y unzip
unzip lab-app.zip -d /var/www/html/

# Enable and start web server (systemd)
systemctl enable httpd
systemctl start httpd

```
Esse script irá: <br>
I. Instalar o Apache e PHP <br>
II. Instalar cliente MySQL/MariaDB <br>
III. Baixar uma aplicação web <br>
IV. Descompactar a aplicação no diretório do Apache<br>
V. Iniciar e habilitar o servidor web<br>

15) Clique em `Executar instância`, após isso será necessário aguardar a instância entrar em execução. 
16) Na aba de instâncias, visualize o estado da sua instância e aguarde até que esteja em execução
17) Clique no check box ao lado da sua instância criada para visualizar os detalhes <br></br>

# Atividade 2 - Monitoramento de instância

18) É possível obter uma captura de tela da instância, no menu `Ações` > **Monitorar e solucionar problemas** > **Obter captura de tela da instância**
<br>
Caso esteja tudo correto, você verá uma tela preta do terminal em booting com a logo da AWS. 
19) Após isso, selecione `cancelar` na parte inferior da captura de tela<br></br>

# Atividade 3 - Gerenciar o grupo de segurança e acessar o servidor WEB

20) Selecionando a instância marcando o checkbox, vá na guia 
**Detalhes**
<br>Caso você copie o endereço público de IPV4 da instância e cole no seu navegador em uma nova guia para pesquisar. (**Não se esqueça de acessar via HTTP e não HTTPS**) Você não conseguirá acessar o servidor web.</br>
21) Para isso, vá em **Grupos de segurança** no painel de navegação, depois em **Redes e Segurança**
22) Selecione o grupo de segurança criado por você no **passo 10** da **etapa 5**
23) Na guia de **Regras de entrada**, selecione o botão `Editar regras de entrada`, depois selecione `Adicionar regra`, definindo da seguinte forma:
+ _Tipo_: HTTP
+ _Origem_: IPV4 em qualquer lugar<br>
Salve as regras no botão `Salvar regras`
24) Dessa forma, você conseguirá acessar a sua instância colando o IPV4 público em seu navegador <br></br>

# Atividade 4 - Redimensionar a instância: Tipo de instância e volume EBS
Agora vamos mudar o tipo da instância, e para isso, vamos interromper a instância. <br>
25) Na aba de **instâncias**, vá em `Estado da instância`> `Parar instância`. Aguarde até que o estado da instância apareça interrompida.

## Etapa 1 - Alterando o tipo da instância
26) No menu, vá em `Ações`> `Configurações de instância` > `Alterar tipo de instância` e defina:
+ Tipo da instância: **t3.small** (esse tipo de instância tem o dobro de memória em relação a t3.micro) <br>

Selecione `Alterar instância`

## Etapa 2 - Redimensionar o volume do EBS 
27) No menu de navegação à esquerda, selecione Volumes em Elastic Block Store (EBS)
28) Selecione o checkbox do volume existente, após isso vá em `Actions` > `Modificar volume`
29) Na aba de **Modificar volume**, mude o tamanho de 8 para 10, e clique em `Modificar` e confirme. 


## Etapa 3 - Iniciar a instância 
30) Retorne a aba de **Instância**, marque o checkbox da instância redimensionada > `Estado da Instância` > `Iniciar instâncias` <br></br>

# Atividade 5 - Testar a proteção contra encerramento da instância

Ao criar a instância, configuramos para que a instância tenha proteção contra encerramento acidental ou forçado, agora vamos testar essa funcionalidade. 

31) Marcando a caixa de seleção da instância, vamos clicar em `Estado da instância` na parte superior do menu > `Encerrar (excluir) instância` > Confirme a exclusão no botão `Encerrar`.
<br></br>
Após essa etapa você verá que não foi possível realizar a exclusão da instância, que é necessário modificar esse atributo da instância e tentar novamente. 
<br> Ou seja, a proteção está funcionando corretamente como configurada.

## Etapa 2 - Encerrando a instância
32) No menu `Ações`, selecione `Configurações de instância`> `Alterar proteção contra encerramento`
33) Desmarque o checkbox de **Ativar** e confirme em `Salvar`
34) No menu `Ações`, selecione `Estado da Instância`> `Terminar instância` e confirme em `Encerrar`.

## Fontes de apoio 📚

[Guia EC2](https://docs.aws.amazon.com/pt_br/AWSEC2/latest/UserGuide/concepts.html)<br>
[Tutorial primeiros passos em EC2](https://docs.aws.amazon.com/pt_br/AWSEC2/latest/UserGuide/EC2_GetStarted.html)<br>
[Uso do nível gratuito para o Amazon EC2](https://docs.aws.amazon.com/pt_br/AWSEC2/latest/UserGuide/ec2-free-tier-usage.html)
<br></br>

<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/9/93/Amazon_Web_Services_Logo.svg" alt="Logo AWS" width="100"/>
  &nbsp;&nbsp;&nbsp;
</p>
