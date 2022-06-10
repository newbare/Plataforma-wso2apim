# Links úteis:

- [WSO2 API MANAGER](https://wso2.com/br/api-manager/#)

- [Binário da Versão 4.1.0 Community](https://github.com/wso2/product-apim/releases/download/v4.1.0/wso2am-4.1.0.zip)


- [Ansible Scripts](https://product-dist.wso2.com/downloads/api-manager/4.1.0/instruction-pages/subscription/ansible/ansible-apim-4.1.0.1.zip)


- [Github GOVBR](http://tools.govbr.com.br/gitlab/jefferson.silva/treinamento-wso2)

- [Instalação Ansible Amazon Linux](https://devcoops.com/install-ansible-on-aws-ec2-amazon-linux/)

- [JAVA 11 AWS](https://docs.aws.amazon.com/corretto/latest/corretto-11-ug/downloads-list.html)
```
wget https://corretto.aws/downloads/latest/amazon-corretto-11-x64-linux-jdk.tar.gz
* OBS: Não esqueça de renomear o nome do arquivo de acordo com o video
```

- [Criar um banco Postgres RDS](https://aws.amazon.com/pt/getting-started/hands-on/create-connect-postgresql-db/?nc1=h_ls)

- [PGADMIN para Docker](https://www.pgadmin.org/docs/pgadmin4/latest/container_deployment.html)

```
docker run -p 80:80 \
    -e 'PGADMIN_DEFAULT_EMAIL=user@domain.com' \
    -e 'PGADMIN_DEFAULT_PASSWORD=SuperSecret' \
    -e 'PGADMIN_CONFIG_ENHANCED_COOKIE_PROTECTION=True' \
    -e 'PGADMIN_CONFIG_LOGIN_BANNER="Authorised users only!"' \
    -e 'PGADMIN_CONFIG_CONSOLE_LOG_LEVEL=10' \
    -d dpage/pgadmin4
```
- [Portainer Community](https://docs.portainer.io/v/ce-2.9/start/install/server/docker/linux)

```
docker volume create portainer_data
```

```
docker run -d -p 8000:8000 -p 9443:9443 --name portainer \
    --restart=always \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v portainer_data:/data \
    portainer/portainer-ce:2.9.3

```

- [Driver Postgres](https://repo1.maven.org/maven2/org/postgresql/postgresql/42.2.10/postgresql-42.2.10.jar)

- [Quakus API do Jefera](https://github.com/newbare/quarkus-wso2)


# WSO2 API Management Ansible scripts

Este repositório contém os scripts Ansible para instalação e configuração do WSO2 API Management.

## Sistemas Operacionais Suportados

- Ubuntu 16.04 or higher
- CentOS 7

## Suporte a versão do Ansible

- Ansible 2.9 ou maior

## Estrutura de Diretórios Ansible
```
.
├── dev
│   ├── group_vars
│   │   └── apim.yml
│   ├── host_vars
│   │   ├── apim-control-plane_1.yml
│   │   ├── apim-gateway_1.yml
│   │   ├── apim-tm_1.yml
│   │   ├── apim_1.yml
│   │   ├── apim_2.yml
│   └── inventory
├── docs
│   ├── images
│   │   ├── 3-fully-distributed-setup.png
│   │   ├── API-M-single-node-deployment.png
│   │   ├── P-H-2.png
│   │   ├── P-H-3.png
│   │   ├── P-M-1.png
│   │   └── P-S-1.png
│   ├── Pattern_1.md
│   ├── Pattern_2.md
│   ├── Pattern_3.md
│   ├── Pattern_4.md
│   └── Pattern_5.md
├── files
│   ├── lib
│   │   ├── amazon-corretto-11.0.14.1-linux-x64.tar.gz
│   └── packs
│   │   ├── wso2am-4.1.0.zip
│   ├── system
│   │   └── etc
│   │       ├── security
│   │       │   └── limits.conf
│   │       └── sysctl.conf
│   └── misc
├── issue_template.md
├── LICENSE
├── pull_request_template.md
├── README.md
├── roles
│   ├── apim
│   │   ├── tasks
│   │   └── templates
│   ├── apim-control-plane
│   │   ├── tasks
│   │   └── templates
│   ├── apim-tm
│   │   ├── tasks
│   │   └── templates
│   └── common
│       └── tasks
├── scripts
│   ├── update.sh
│   └── update_README.md
└── site.yml

```

As instruções a seguir podem ser seguidas para uma implantação de APIM padrão tudo-em-um( All in one) para implantar o produto ou para fins de demonstração. Se você deseja  um padrão de implantação pronto para produção, consulte a documentação no diretório `docs`.

## Copiando pacotes localmente
Os pacotes podem ser copiados para um diretório local ou baixados de um local remoto.

Copie os seguintes arquivos para o diretório `files/packs`.


1. [WSO2 API Manager 4.1.0 package (.zip)](https://wso2.com/api-management/install/)

Copie os seguintes arquivos para o diretório `files/lib`.

1. [Amazon Corretto for Linux x64 JDK 11 (.tar.gz)](https://docs.aws.amazon.com/corretto/latest/corretto-11-ug/downloads-list.html)

Copie os arquivos diversos para o diretório `files/misc`. Para habilitar a cópia de arquivos, descomente o `misc_file_list` nos arquivos yaml em `group_vars` e adicione os arquivos diversos à lista.

## Downloading de um local remoto

Em **group_vars**, altere os valores das seguintes variáveis em todos os grupos:
1. O valor de `pack_location` deve ser alterado de "local" para "remote"
2. O valor de `remote_jdk` deve ser alterado para o URL no qual o JDK deve ser baixado e removê-lo como um comentário.
3. O valor de `remote_pack` deve ser alterado para o URL no qual o pacote deve ser baixado e removê-lo como um comentário.

## Executando scripts Ansible do WSO2 API Management

### 1. Execute os scripts existentes sem personalização

Os scripts do Ansible existentes contêm as configurações para definir um padrão do WSO2 API Manager de nó único. Para implantar o padrão, você precisa substituir o `[ip_address]` e o `[ssh_user]` fornecidos no arquivo `inventory` na pasta `dev` pelo IP do local onde você precisa hospedar o API Manager. Um exemplo é dado abaixo.

```
[apim]
apim_1 ansible_host=172.28.128.4 ansible_user=vagrant
```

Em seguida, atualize o nome do host em dev/host_vars/apim_1.yml com ansible_host, 172.28.128.4

Execute o seguinte comando para executar os scripts. Use `-K` para fornecer uma senha para se tornar root. Configure `become_method:` em `ansible.cfg` ao usar uma alternativa para
`sudo`. Se o seu host permite `sudo` sem senha, então omita o argumento `-K`.
`ansible-playbook -K -i dev site.yml`

Se você precisar alterar as configurações fornecidas, altere os valores parametrizados nos arquivos yaml em `group_vars` e `host_vars`.

**NOTA:**
> Se você montou os 'artefatos persistentes' conforme guiado [abaixo](##configuration-guide), certifique-se de desmontar os artefatos antes de executar o playbook do Ansible, pois o processo de execução do playbook tem uma etapa para remover a configuração existente. Depois de concluir o processo de execução do playbook do Ansible, certifique-se de remontar os artefatos.

> Se o `client-truststore.jks` estiver montado entre os nós do Gateway, certifique-se de copiar o `client-truststore.jks` da fonte de montagem para o diretório `<ANSIBLE_HOME>/files/security/wso2am/` do os recursos do Ansible, antes de executar novamente o playbook.

### 2. Personalize os scripts do WSO2 Ansible

Os modelos usados pelos scripts Ansible estão no formato j2 para permitir a parametrização.

O arquivo `deployment.toml.j2` é adicionado em `roles/apim/templates/carbon-home/repository/conf/`, para permitir personalizações. Você também pode adicionar outras personalizações ao `custom.yml` nas tarefas de cada função.

#### Passo 1
Remova o comentário da linha a seguir em `main.yml` na função que você deseja personalizar.
```
- import_tasks: custom.yml
```

#### Passo 2
Adicione as configurações ao `custom.yml`. Uma amostra é dada abaixo.

```
- name: "Copiar arquivo personalizado"
  template:
    src: path/to/example/file/example.xml.j2
    dest: destination/example.xml.j2
  when: "(inventory_hostname in groups['am'])"
```

Siga as etapas mencionadas no diretório `docs` para personalizar/criar novos scripts Ansible e implantar os padrões recomendados.

#### Incluindo Keystore e Truststore personalizados
Se for necessário adicionar keystores e truststores personalizados, remova o comentário da lista abaixo no arquivo yml
```
# security_file_list:
#   - { src: '{{ security_file_location }}/wso2am-analytics/client-truststore.jks',
#       dest: '{{ carbon_home }}/resources/security/client-truststore.jks' }
#   - { src: '{{ security_file_location }}/wso2am-analytics/wso2carbon.jks',
#       dest: '{{ carbon_home }}/resources/security/wso2carbon.jks' }
```
Deixe na forma abaixo
```
security_file_list:
  - { src: '{{ security_file_location }}/wso2am/client-truststore.jks',
     dest: '{{ carbon_home }}/repository/resources/security/client-truststore.jks' }
  - { src: '{{ security_file_location }}/wso2am/wso2carbon.jks',
     dest: '{{ carbon_home }}/repository/resources/security/wso2carbon.jks' }
```

Em seguida, salve o arquivo alterado e adicione os arquivos necessários em `files/security/<product-home>/<path-to-file>`

##### Modificando a senha do Keystore 
Antes de começar copie os arquivos do diretório `default para o diretório wso2am`

1. Em `<home-diretorio>/files/security` navegue até o subdiretorio chamado `wso2am`e dentro dele crie um diretório `default`para os certificados originais.
2. No binário do wso2apim que você realizou o Download e descompactou de acordo com o video, copie os certificados para dentro da pasta `default` são eles:
`cliente-trustecore.jks e wso2carbon.jks`ambos por padrão possuem usuario e senha `wso2carbon` e não seria legal deixar essa configuração de segurança como padrão.
3. Ulilize o Java keytool `JAVA_HOME/bin/keytool`
  3.1. listando os certificados

```
sudo keytool -v -list -keystore wso2carbon.jks -alias wso2carbon -storepass wso2carbon
sudo keytool -v -list -keystore client-truststore.jks -alias wso2carbon -storepass wso2carbon

```
  3.2. mudar senha de um alias - OBS(anote esta senha para atualizar o script) 
  ex.: `supersenha`

```

sudo keytool -storepasswd  -alias wso2carbon -keystore wso2carbon.jks
sudo keytool -keypasswd  -alias wso2carbon -keystore wso2carbon.jks
sudo keytool -storepasswd  -alias wso2carbon -keystore client-truststore.jks

```
- Liste os certificados com a nova senha no passo 3.1 passando a nova senha




## Performance Tuning

As configurações do sistema podem ser alteradas por meio do Ansible para otimizar o desempenho no nível do sistema operacional. O ajuste de desempenho pode ser ativado alterando `enable_performance_tuning` em `dev/group_vars/apim.yml` para `true`.

Os arquivos do sistema que serão atualizados quando o ajuste de desempenho estiver ativado estão disponíveis em `files/system`. Atualize os valores de configuração de acordo com os requisitos de sua implantação.

## Guia de Configuração

Consulte a documentação abaixo sobre como configurar armazenamentos de chaves para APIM e APIM-Analytics
1. [WSO2 API Manager key-stores guia de configuração](https://apim.docs.wso2.com/en/latest/install-and-setup/setup/security/configuring-keystores/configuring-keystores-in-wso2-api-manager/)

Consulte a documentação abaixo sobre como configurar artefatos persistentes dos servidores.
1. [Artefatos persistentes dos servidores](https://apim.docs.wso2.com/en/latest/install-and-setup/setup/reference/common-runtime-and-configuration-artifacts/)

Consulte a documentação abaixo sobre como configurar Load-Balancers para sua implantação.
1. [Configurações de Load balancer](https://apim.docs.wso2.com/en/latest/install-and-setup/setup/setting-up-proxy-server-and-the-load-balancer/configuring-the-proxy-server-and-the-load-balancer/)

## Versões anteriores do Ansible

A ramificação mestre deste repositório contém a versão mais recente do produto com a versão mais recente do Ansible. Os recursos do Ansible para versões anteriores do Ansible podem ser encontrados nas ramificações. Veja o exemplo abaixo.

#### Recursos do Ansible para API Manager 4.0.0


Branch name: 4.0.x







## Curso AWS
[Eduardo Lara](https://www.udemy.com/course/preparatorio-exame-aws-certified-cloud-practitioner-clf-c01/)

## Target Group AWS para ALB
[Certificado auto assinado](https://medium.com/@francisyzy/create-aws-elb-with-self-signed-ssl-cert-cd1c352331f)
`helth check APIM curl -v http://ec2-44-201-113-40.compute-1.amazonaws.com:9443/services/Version`

[matcher](https://www.sslshopper.com/certificate-key-matcher.html)

LB-AWS-WSO2-665612561.us-east-1.elb.amazonaws.com
lb-aws-wso2-665612561.us-east-1.elb.amazonaws.com

1. Gere o certificado autoassinado usando este comando:

`openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt`

2. Verifique a chave e o certificado gerados
`openssl rsa -in privateKey.key -check`
`openssl x509 -in certificate.crt -text -noout`

3. Converta a chave e o certificado em arquivo codificado .pem
`openssl rsa -in privateKey.key -text > private.pem`
`openssl x509 -inform PEM -in certificate.crt > public.pem`



