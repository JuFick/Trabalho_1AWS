# Trabalho_1AWS
Repositório para a atividade do programa de bolsas da Compass UOL - AWS + Linux 

## Atividade Prática Individual
### Requisitos AWS:
- Gerar uma chave pública para acesso ao ambiente;
- Criar 1 instância EC2 com o sistema operacional Amazon Linux 2 
    (Família t3.small,16 GB SSD);
- Gerar 1 elastic IP e anexar à instância EC2;
- Liberar as portas de comunicação para acesso público:
    (22/TCP, 111/TCP e UDP, 2049/TCP/UDP, 80/TCP, 443/TCP).

### Requisitos no linux:
- Configurar o NFS entregue;
- Criar um diretorio dentro do filesystem do NFS com seu nome;
- Subir um apache no servidor - o apache deve estar online e rodando;
- Criar um script que valide se o serviço esta online e envie o resultado da validação
para o seu diretorio no nfs;
  - O script deve conter - Data HORA + nome do serviço + Status + mensagem personalizada de ONLINE ou offline;
  - O script deve gerar 2 arquivos de saida: 1 para o serviço online e 1 para o serviço OFFLINE;
  - Preparar a execução automatizada do script a cada 5 minutos.

---

## Passos do Desenvolvimento

### Criação da chave de acesso:
- Acesse o ambiente AWS e entre no servico EC2;
- No canto esquerdo haverão várias opções de configuração de serviço, selecione **Rede e Segurança** e, em seguida, selecione **Pares de Chave**;
- No canto superior direito, destacado em laranja, clique em **Criar par de chaves**;
- Nomeie sua chave, selecione o arquivo .Pem, e no canto inferior direito, destacado em laranja, clique em **Criar par de chaves**.

### Criação da Instância EC2:
- No menu de configurações do lado esquerdo, clique em **Instâncias** e em **Instâncias** novamente;
- No canto superior diretio, clique no botão laranja **Executar Instância**;
- Nomeie sua Instância;
- Selecione a imagem-AMI (Amazon Linux 2 AMI (HVM), SSD Volume Type);
- Selecione o tipo de instância (t3.small);
- Selecione a chave gerada anteriormente;
- Selecione o armazenamento (16 GB gp2 SSD);
- Clique no botão inferior laranja direito **Executar instância**.

### Gerar 1 elastic IP e Anexar à instância EC2:
- No menu de configurações do lado esquerdo, selecione **Rede e Segurança** e, em seguida, selecione **IPs elásticos**;
-Clique no botão laranja superior direito **Alocar endereço IP elástico**;
-Selecione o ip alocado e clique no botão superor branco **Ações** e, em seguida clique em **Associar endereço IP elástico**;
- Selecione a instância EC2 criada anteriormente e clique no botãoinferior direito laranja **Associar**.

### Configuração gateway de internet (possibilita comunicação com a internet):
- Acesse o ambiente AWS e entre no serviço de VPC;
- No menu lateral esquerdo, clique em **Nuvem Privada Virtual** e depois em **Gateways de internet**;
- Clique no botão laranja **Criar Gateway de Internet**;
- Defina um nome para o gateway e clique no botão inferior direito **Criar gateway de internet**;
- Selecione o gateway criado e clique no botão branco **Ações**;
- Clique em **Associar à VPC**;
- Selecione a VPC da instância EC2 previamente criada e clique em **Associar**.

### Configurar rota de internet:
- Acesse o ambiente AWS e entre no serviço de VPC;
- No menu lateral esquerdo, clique em **Nuvem Privada Virtual** e depois em **Tabelas de Rotas**;
- Selecione a tabela de rotas da VPC da instância EC2 criada anteriormente;
- Clique no botão branco **Ações** e, em seguida clique em **Editar rotas**;
- Clique em **Adicionar rota**;
- Configure o *Destino* como 0.0.0.0/0 e o *Alvo* com o gateway de internet criado anteriormente;
- Clique em **Salvar alterações**.

### Liberação de Portas:
- Acesse a AWS e entre no serviço de EC2;
- No menu de configurações do lado esquerdo, selecione **Rede e Segurança** e, em seguida, selecione **Grupos de Segurança**;
- Selecione o grupo de segurança vinculado a sua instância criada;
- Clique no botâo branco de **Ações** e, em seguida **Editar regras de entrada**; 
- Clique em **Adicionar regra** no botão inferior esquerdo branco e configure as portas solicitadas: 22/TCP, 111/TCP e UDP, 2049/TCP/UDP, 80/TCP, 443/TCP, que são respectivamente as portas de SSH, RPC, NFS, HTTP e HTTPS;
- Clique em **Salvar regras** no botão laranja inferior direito.

### Acesso à plataforma
- Localize seu arquivo de chave privada e execute o comando `chmod 400 SuaChave.pem`;
- Acesse seu hambiente AWS através da porta de ssh utilizando o comando: ` ssh -i "SuaChave.pem" ec2-user@ec2-sua-dns-publica`.

### Configuração do NFS
- Verifique se o NFS já está instalado na sua máquina EC2 com o comando `systemctl nfs-server.service`;
- Se for necessária a instalação, execute os comandos `sudo yum install nfs-utils -y` e, em seguida inicie o nfs com o comanado `sudo systemctl start nfs-server`;
*Lembre-se de estar executando os comandos como root*
- Acesse o diretório /mnt com a sequência de comandos `cd /` > `ls` > `cd /mnt`;
- Crie um diretório NFS dentro do /mnt que possua permissão total com o comando `sudo mkdir nfs -m 777`;
- Crie um diretório com seu nome dentro do diretório nfs que também possua permissão total;
- Torne o compartilhamento NFS global para o diretório nfs com os seguintes passos: `cd /` > `ls` > `cd /etc` > `vim exports` > `i` > `/nfs *(rw)` > aperte o botão `esc` > `:q!`;
- Reinicie e habilite o serviço `systemctl restart nfs-server` > `systemctl enable nfs-server`;

### Instalação do Apache
- Verifique se o serviço já está instalado com  `systemctl status httpd`;
- Se for necessária a instalação, execute o comando `sudo yum install httpd`;
- Inicie e habilite o serviço com os comandos `sudo systemctl start httpd` > `sudo systemctl enable httpd`;
- Verifique se o serviço está habilitado `systemctl status httpd`;


