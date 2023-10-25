# Integração do SEI com o Diário Oficial da União (INCOM)  
  
# Introdução  
O módulo de integração do SEI com o INCom, tem como objetivo permitir a publicação de documentos diretamente no Diário Oficial da União - DOU da Imprensa Nacional - IN. Esta interoperabilidade somente foi possível através da implementação de serviços que permitem a publicação de matérias em HTML, formato utilizado pelo SEI em seus documentos internos. O conteúdo desta transmissão conterá todas as informações de referência da matéria e do ofício, bem como o próprio conteúdo a ser publicado, com as devidas indicações dos estilos a serem aplicados.  
  
Todo o desenvolvimento do módulo se baseou nos seguintes documentos e normativos da própria Imprensa Nacional:  
* Documentação dos Serviços de Envio de Matérias para Imprensa Nacional - Web Service INCOM  
* Decreto Nº 9.215, de 29 de novembro de 2017
* Portaria Nº 268, de 5 de outubro de 2009  
* Portaria Nº 188, de 7 de julho de 2011  
* Portaria Nº 258, de 13 de novembro de 2013  
  
Este manual está estruturado nas seguintes seções:  
  
1. **Instalação**  
Procedimentos de instalação do módulo no servidor web e atualização de banco de dados.
  
2. **Configuração**  
Orientações voltadas para o administrador do sistema configurar os parâmetros do módulo para o correto funcionamento da integração.  

3. **Cadastramento**  
Apresentação das funcionalidades que permitem o cadastro do usuário para publicar no DOU.

4. **Utilização**  
Apresentação das funcionalidades que permitem o trâmite externo de processos e o acompanhamento de seus históricos.

5. **Problemas Conhecidos**  
Problemas comuns já reportados durante o uso do módulo e seus respectivos procedimentos para correção.  

6. **Suporte**  
Canais de comunicação para resolver problemas ou tirar dúvidas sobre o módulo.
  
# 1 - Instalação  
  
## 1.1 Pré-requisitos  

* SEI versão 3.0.X ou superior instalada;
* Usuário de acesso ao banco de dados do SEI e SIP com permissões para criar novas tabelas;

## 1.2 Autorização para acesso aos serviços de integração do DOU;
Para que o SEI do órgão possa enviar matéria para o sistema INCom da Imprensa Nacional, é necessário que SEI do órgão seja cadastrado no INCom. Para tanto, o responsável pelo SEI na área de tecnologia do órgão deve encaminhar  solicitação à Imprensa Nacional  conforme modelo do [anexo I](https://github.com/spbgovbr/mod-sei-incom/blob/master/src/docs/ANEXOI.docx). O responsável na área de TI receberá login e senha para configurar o acesso do módulo do SEI ao INCOM. 

  
## 1.3 Procedimentos de Instalação  
  
#### 1.3.1 - Cópias de segurança e backups do banco de dados  
Por questões de segurança, deve-se realizar o backup dos bancos de dados SEI e SIP, assim como a cópia de segurança dos arquivos que compõe a aplicação SEI. Este procedimento é requerido para ser possível a restauração do sistema em caso de falha nos procedimentos de instalação.
  
#### 1.3.2 - Cópia da pasta do módulo  
Mover o diretório de arquivos do módulo “mod-sei-incom” para o diretório sei/web/modulos/ Importante renomear a pasta do módulo “mod-sei-incom” para somente “incom” por questões de padronização de nomenclatura.
  
#### 1.3.3 - Configuração do módulo no arquivo ConfiguracaoSEI.php  
Editar o arquivo **sei/config/ConfiguracaoSEI.php**, tomando o cuidado de usar editor que não altere o charset ISO 5589-1 do arquivo, para adicionar a referência ao módulo INCom na chave **[Modulos]** abaixo da chave **[SEI]**:  
  
	'SEI' => array(  
		'URL' => 'http://[servidor sei]/sei',  
		'Producao' => true,  
		'RepositorioArquivos' => '/var/sei/arquivos',  
		'Modulos' => array('MdIncomIntegracao' => 'incom'),  
	),  
  
Adicionar a referência ao módulo INCom na array da chave 'Modulos' indicada acima:  
  
	'Modulos' => array('MdIncomIntegracao' => 'incom')  
  
#### 1.3.4 - Criação das tabelas de banco de dados  
Para realizar a instalação das tabelas do módulo INCom, será necessário executar os scripts de manutenção da base de dados localizados na raiz da diretório **[modulos/incom]**.  

##### 1.3.4.1 Atualização da base de dados do SEI  
Primeiramente, será necessário mover o arquivo de atualização do banco SEI para a pasta **[sei/scripts]**. Este arquivo de atualização está localizado no diretório **[incom/scripts/sei_atualizar_versao_modulo_incom.php]**  
Lembre-se de mover, e não copiar o arquivo, por questões de segurança e padronização.  
  
	mv sei/web/modulos/incom/scripts/sei_atualizar_versao_modulo_incom.php sei/scripts/  
  
Após mover o script para o diretório correto, será necessário executá-lo para criar as tabelas necessárias para seu funcionamento. Mova para o diretório de scripts do **SEI** e execute o seguinte comando:  
  
	php -c /etc/php.ini sei_atualizar_versao_modulo_incom.php  
  
##### 1.3.4.2 Atualização da base de dados do SIP  
Neste passo também será necessário mover o arquivo de atualização do banco SIP para a pasta **[sip/scripts]**. Este arquivo de atualização está localizado no diretório **[incom/scripts/sip_atualizar_versao_modulo_incom.php]**.
Lembre-se de mover, e não copiar o arquivo, por questões de segurança e padronização.  
  
	mv sei/web/modulos/incom/scripts/sip_atualizar_versao_modulo_incom.php sip/scripts/  
  
Após mover o script para o diretório correto, será necessário executá-lo para que o módulo crie ou atualize as tabelas necessárias para seu funcionamento. Mova para o diretório de scripts do **SIP** e execute o seguinte comando:  
  
	php -c /etc/php.ini sip_atualizar_versao_modulo_incom.php  
  
> **Atenção!**  
> Após a instalação do módulo, o usuário de manutenção deverá ser alterado para outro contendo apenas as permissões de leitura e escrita no banco de dados.  
  
#### 1.3.5 - Instalação de Certificado Digital do INCOM
Para consumir os serviços da Imprensa Nacional é necessário a obtenção do certificado SSL do INCom que tem a função de certificar que o endereço do Webservice é realmente da Imprensa Nacional. O certificado pode ser feito obtido no seguinte endereço: [https://incom.in.gov.br/](https://incom.in.gov.br/)
Para proceder a instalação deste certificado, os seguintes passos deverão ser executados em todos os servidores de aplicação do SEI, inclusive aquele responsável pelo gerenciamento dos agendamentos de tarefas do sistema.

Passo 1 - Copie o certificado utilizado pelo INCom para o diretório /usr/local/share/ca-certificates:
	
	cp <CADEIA-CERTIFICADO-CA> /usr/local/share/ca-certificates

Passo 2 - Efetue a atualização da lista de certificados confiáveis do sistema operacional
	
	sudo update-ca-certificates
  
# 2 - Configuração  
As seções abaixo descrevem os passos complementares da instalação necessários para o correto funcionamento do módulo de publicação no DOU.  

## 2.1 - Configurações Gerais  
O primeiro passo da configuração é acessar a página de parâmetros gerais do módulo em que serão informados dados de integração do SEI com o sistema INCom. Estas configurações ficarão disponíveis apenas para os usuários com perfil de Administração no SEI e pode ser encontrado no menu **[ SEI >> Administração >> Diário Oficial da União]**.  

![Página de configurações gerais](https://github.com/spbgovbr/mod-sei-incom/blob/master/src/imagens/configuracao-incom.JPG)

Lista de parâmetros:  

1. **Veículo de Publicação**  
Veículo utilizado pelo módulo para realizar a integração no Diário Oficial da União. Este veículo é criado automaticamente durante a instalação, mas pode ser modificado pelo administrador através deste parâmetro.  
  
2. **Web Service**  
Endereço do Web Service fornecido pela Imprensa Nacional para acesso aos serviços de integração do INCOM. Estes dados de acesso ao INCOM são pré-requisitos para a configuração deste módulo, maiores informações podem ser obtidas na seção *Instalação > Pré-requisitos > Autorização para acesso aos serviços de integração do DOU*.  
Segue lista dos endereços dos webservices para cada ambiente do INCOM:  
  
	- *Homologação*  
https://seiwsincom2.in.gov.br/seiwsincom/services/servicoIN?wsdl  
Utiliza autenticação mútua.  
  
	- *Produção*  
https://seiwsincom.in.gov.br/seiwsincom/services/servicoIN?wsdl  
Utiliza autenticação mútua.  
  
3. **Usuário e Senha do Serviço**  
Usuário e senha para uso do web service de integração do INCom. Como no parâmetro anterior, os dados de acesso são pré-requisitos para a configuração deste módulo, maiores informações podem ser obtidas na seção *Instalação > Pré-requisitos > Autorização para acesso aos serviços de integração do DOU*.  
  
4. **Testar Conexão**  
Ação para testar o correto funcionamento do web service de integração com o INCom, validando endereço, usuário e senhas do serviço. Caso o serviço não seja validado, o motivo do erro será reportado ao usuário.
  
5. **Tipo de Documento INCom**  
Este é o tipo de documento do SEI que será utilizado pelo módulo ao adicionar, na árvore do processo, o documento PDF de confirmação da publicação final no DOU.

6. **Id Siorg do Órgão** 
Código do órgão na estrutura Siorg, o código pode ser conseguido no seguinte endereço: https://siorg.planejamento.gov.br/siorg-cidadao-webapp/pages/listar_orgaos_estruturas/listar_orgaos_estruturas.jsf.
https://siorg.gov.br/siorg-cidadao-webapp/resources/app/consulta-estrutura.html

8. **Incluir publicação na árvore do processo**  
Caso esta opção esteja marcada, um anexo em pdf será incluído no processo com a publicação feita na imprensa nacional.
  
## 2.2 - Veículo de Publicação  
Após a configuração dos parâmetros gerais de integração, é necessário configurar o veículo utilizado na publicação dos documentos no DOU. As parametrizações necessárias para este veículo são de uso exclusivo do INCom é obrigatório para concretizar a publicação no DOU.
  
Ao realizar a instalação do módulo, um veículo de publicação é criado e pré-configurado automaticamente para uso no SEI. Seus dados precisam ser modificados, conforme descrito abaixo, para habilitar a integração.
  
![Página de lista veículo de publicação](https://github.com/spbgovbr/mod-sei-incom/blob/master/src/imagens/9d853c5a-9902-4104-a09d-afc2912d48df.jpg)

Lista de parâmetros:  
  
1. **Nome**  
Nome do veículo de publicação  
  
2. **Descrição**  
Descrição do veículo de publicação  
  
3. **Tipo**  
Tipo de veículo de publicação, sendo que as opções possíveis são Interno, Externo e Módulo. Para questões de configuração do módulo de integração do SEI com o INCOM, a opção utilizada deve ser Módulo.  
  
4. **Exibir as publicações enviadas para este veículo na pesquisa de publicações interna**  
Marcando esta opção, todos os documentos publicados neste veículo também estarão disponíveis para busca na pesquisa de publicações internas do SEI.  
  
5. **Utilizar os feriados cadastrados neste veículo como padrão para o sistema**  
Possibilita informar qual veículo será fonte de feriados para verificação durante o agendamento da publicação. Se o veículo Interno for a fonte de feriados então é necessário cadastrar os feriados no SEI (menu Administração/Publicação/Feriados). Caso o veículo de publicação no DOU esteja configurado como padrão de cadastro de feriados para o sistema, o calendário de feriados da Imprensa Nacional passará a ser utilizada como referência para todo o sistema.  
  
## 2.3 - Associação de Unidades Gestoras  
Outro passo obrigatório para publicação no DOU é configurar quais são as unidades gestoras que foram habilitadas para publicação junto à Imprensa Nacional. Este cadastro é feito no INCom e deve ser feito antes da configuração desta associação.  

![Página de lista de veículos de publicação](https://github.com/spbgovbr/mod-sei-incom/blob/master/src/imagens/6836a3c9-f06f-42e2-ab2c-f9135888a71a.jpg)  
  
O cadastramento do veículo de publicação do INCom habilita uma séria de novas ações para configuração da integração. Entre as novas opções, estará disponível a ação Associação de Unidades, conforme apresentado acima.  

![Página de lista de associação de unidades](https://github.com/spbgovbr/mod-sei-incom/blob/master/src/imagens/incom_manual_unidades_associadas.PNG)

Nesta página deverá ser apresentado a lista de unidades habilitadas no INCom para realizar a publicação de documentos. Todas as unidades habilitadas deverão ser cadastradas através da acionamento da opção **[1] Novo** que apresentará a seguinte pagina de cadastro.

![Página de associação de unidades](https://github.com/spbgovbr/mod-sei-incom/blob/master/src/imagens/incom_manual_unidades_nova_associacao.PNG)

1. **Código Siorg**  
Código da Unidade Administrativa no SIORG para posicionamento da matéria no espelho do Jornal. Caso seja digitado o código do siorg diretamente neste campo, o campo "Nome da Unidade no Siorg" irá ser preenchido automaticamente

2. **Nome da Unidade no Siorg**
Caso o usuário não saiba o código do siorg, ele poderá utilizar esse campo para selecionar na árvore qual a unidade administrativa do Siorg responsável pelo posicionamento da matéria no espelho do Jornal. Na tela abaixo pode ser visualizada a tela que é gerada após clicar na lupa para seleção de unidade.
![Página de Nome da Unidade no Siorg](https://github.com/spbgovbr/mod-sei-incom/blob/master/src/imagens/selecao_orgao_siorg.PNG)

3. **Unidade do SEI**
Unidade administrativa da instituição cadastrada no SEI.

4. **Código de Unidade Gestora**  
Código de unidade gestora correspondente. Este campo é obrigatório se o pagamento for através de nota de empenho. A Unidade Gestora é a responsável por gerir recursos orçamentários e financeiros para publicação da matéria. Em caso de pagamento com boleto ou boleto avulso não é obrigatório.

5. **Empenho Sugerido**  
Número de Empenho que irá ser mostrado ao publicar-se algum documento no Incom, usando a modalidade de tipo de pagamento: Empenho ou Nota de Crédito.
Ele é sugerido pois o publicador pode alterá-lo, se desejar, no momento da publicação.
  
## 2.4 - Associação de Atos para Documentos  
Além da associação das unidades gestoras, também é obrigatório associar os Tipos de Documentos do SEI à lista pré-definida de Atos do DOU.  Esta associação deve ser feita acionando a opção **Documento Associados** na lista de veículos de publicação, conforme apresentado abaixo:

![Página de lista de veículos de publicação](https://github.com/spbgovbr/mod-sei-incom/blob/master/src/imagens/53ba08d0-f6b6-4ca5-9a1f-8d9646dc28cb.jpg)


![Página de lista de documentos associados](https://github.com/spbgovbr/mod-sei-incom/blob/master/src/imagens/049b3684-fed1-4bb9-b163-57b4168d47be.jpg)
Nesta página deverá ser apresentada a lista de associações entre os Tipos de Documentos e os Atos Oficiais pré-definidos pelo INCom. Todas as associações deverão ser cadastradas através da acionamento da opção **[1] Novo** que apresentará a seguinte pagina de cadastro.
  ![Página de nova associação de documentos](https://github.com/spbgovbr/mod-sei-incom/blob/master/src/imagens/248c4f02-87c8-41f9-aed3-cd4c05139c07.jpg)
   
1. **Tipo de Documento do SEI**
Tipo de Documento interno do SEI, sendo que somente aqueles com aplicabilidade "Interna" ou "Interna e Externa" podem ser selecionados, pois somente documentos internos gerados pelo SEI poderá ser publicados no DOU.

2. **Ato Normativo do DOU**
Ato oficial que será vinculado ao tipo de documento do SEI.

### 2.4.1 - Seções do Documento para Publicação  
Após a associação de um Tipo de Documento para um Ato do DOU, a nova ação **[1] Seções do Documento para Publicação** é apresentada na lista de Associações de Documentos. Esta opção permite ao administrador definir quais as seções do documento serão enviadas para publicação no DOU.  
    ![Página de lista de documentos associados](https://github.com/spbgovbr/mod-sei-incom/blob/master/src/imagens/bb9b2aa1-5a6f-49af-a71e-8815cdfe4b0b.jpg)

Esta opção permitirá a configuração de quais seções do documento do SEI serão enviadas e publicadas no DOU. Além de identificar quais seções serão publicadas, também é necessário indicar para o INCOM qual o tipo da seção, sendo possíveis os valores: **Título**, **Ementa**, **Conteúdo**.

![Página de configuração de seções do documento](https://github.com/spbgovbr/mod-sei-incom/blob/master/src/imagens/780dab9f-de99-441c-a72e-20db41a6c197.jpg)

 1. Lista de seções do módulo de documento vinculado
 2. Lista de mapeamentos para as seções presentes no INCOM
 3. Configuração do mapeamento. Aquelas seções configuradas como *\<Sem relacionamento\>* não serão enviadas para publicação.

#### Importante!

A assinatura poderá ser mapeada nessa tela para envio automático, ou poderá ser deixada em branco:
- caso a assinatura tenha sido mapeada, então ao enviar a publicação para o Incom, o SEI incluirá o nome e o cargo de cada pessoa que assinou o documento no corpo da publicação;
- caso a assinatura não tenha sido mapeada, então o confeccionador do documento deverá adicionar o(s) nome(s) e cargo(s) do(s) assinante(s) no corpo do documento.

Como mencionado anteriormente, as seções do documento que necessitam de destaque no INCom são: Título, Conteúdo e Ementa. Visto que na maiorias dos modelos de documentos que possuem Ementa, como a Portaria, precisam que esta seção seja destacada no modelo do documento. Com isto, é necessário alterar os modelos que possuem ementa para separar o texto da ementa do corpo principal do documento.

Para aplicar esta configuração, os seguintes passos precisam ser realizados(nesta configuração será utilizado o documento do tipo Portaria):

#### I - Criar Nova Seção Ementa
Acessar a página de configuração das seções do modelo de documento através do menu [Administração >> Editor >> Modelos >> Listar], ícone de ação "Seções do Modelo".

![Página de nova seção do modelo](https://github.com/spbgovbr/mod-sei-incom/blob/master/src/imagens/94099c97-e182-4e86-b08f-bc3a75a9e41d.jpg)

Esta funcionalidade lista todas as seções do modelo que serão utilizadas para construir a versão inicial do documento. Para os tipos de documento que possuem Ementa [1], será necessário adicionar esta nova seção entre o Título do Documento e o Corpo do Texto, utilizando o campo ordem para definir seu posicionamento [2]. O conteúdo desta seção deverá ser um texto introdutório para da ementa [4] e ter somente a opção "Conteúdo HTML" habilitada [3] e o conjunto de estilos possíveis de formatação adicionados conforme as demais seções do documento [5]. 
Importante lembrar que o texto introdutório adicionado deverá ser removido da outra seção Corpo do Texto para não gerar textos duplicados na geração de novos documentos.

#### II - Configurar a nova associação de documento
Após esta mudança no modelo dos documentos que possuem uma seção de ementa, a associação deverá ser aplicada nas configurações descritas no item 2.4.1 - Seções do Documento para Publicação  


## 2.5 - Tipos de Documentos para Publicação  
Para configurar quais os tipos de documento de SEI que poderão ser publicados via Veículo de Publicação do Diário Oficial da União, será necessário selecionar no cadastro de tipos de documentos o veículo gerado pelo módulo de integração. Esta configuração deve ser feita através da funcionalidade **[SEI >> Administração >> Tipos de Documentos]**, campo **[1] Veículos de Publicação**.  
  
![Página de edição do tipo de documento](https://github.com/spbgovbr/mod-sei-incom/blob/master/src/imagens/920af60b-acfc-4401-91dd-c5a47503ac49.jpg)  

## 2.6 - Perfil de publicador no INCOM
Para que os usuários possam publicar matérias no DOU é necessário conceder um perfil para tal. Assim é necessário criar no SIP o perfil de publicador de matérias no DOU com os recursos mínimos mostrados abaixo:

![Página de perfil publicador](https://github.com/spbgovbr/mod-sei-incom/blob/master/src/imagens/montar_perfil_publicador.png)  

![Página de perfil publicador](https://github.com/spbgovbr/mod-sei-incom/blob/master/src/imagens/perfil_publicador.jpg)  

# 3 - Cadastramento do usuário para publicar no DOU
Para que um usuário possa publicar um ato no Incom é necessário que seja feito o cadastro no Incom na Imprensa Nacional, tenha o CPF no cadastro do SEI e tenha o perfil de publicador.

## 3.1 - Cadastro no INCOM
Além da autenticação do sistema, é necessário identificar os usuários que farão a publicação das matérias no DOU. Somente os usuários cadastrados poderão fazer a publicação de matérias no DOU.

O responsável do órgão para envio das matérias atuais no INCom deve solicitar á Imprensa Nacional o cadastramento dos usuários que farão publicação de matérias no DOU conforme modelo [anexo II](https://github.com/spbgovbr/mod-sei-incom/blob/master/src/docs/ANEXOII.docx).

## 3.2 - Cadastro no SEI
O cadastro do usuário no SEI deve ter  o campo de CPF preenchido.

![Página de cadastro no sei](https://github.com/spbgovbr/mod-sei-incom/blob/master/src/imagens/cadastro_no_sei.png) 

## 3.3 - Perfil de Publicador
O perfil de publicador deve ter sido criado na configuração do SEI utilizando o SIP.

Para que o usuário publique no DOU é necessário conceder o perfil de publicador no SIP, no menu de Permissões/Administradas.

Lembrando que o usuário será publicador no DOU na unidade em que lhe foi concedido o perfil de Publicador INCom

![Página de perfil publicador](https://github.com/spbgovbr/mod-sei-incom/blob/master/src/imagens/perfil_publicador_sip.png) 

# 4 - Utilização

[Manual do Usuário](https://github.com/spbgovbr/mod-sei-incom/blob/master/README_USUARIO.md)

# 5 - Problemas Conhecidos 

## Erro Timeout ao executar o agendamento MdIncomAgendamentoRN :: atualizaEstadoPublicacaoIncom

Existem situações em que a qunatidade de pendências de publicação retornadas pelo INCOM geram um volume muito grande de atualizações, o que pode provocar erros de timeout devido o limite de tempo de resposta configurado no servidor web. Caso ocorra o erro mencionado, edite as configurações do agendamento citado para adicionar o atributo "lote" no campo "Parâmetros", indicando a quantidade de pendências que serão processadas a cada execução do agendamento.

```
lote=20
```


# 6 - Suporte
Para maiores informações sobre a integração do SEI com o INCOM da Imprensa Nacional ou contato para suporte à instalação e uso deste módulo, entre em contato com o MP através do Portal de Serviços do Processo Eletrônico Nacional no seguinte link:
http://portaldeservicos.planejamento.gov.br/citsmart/pages/smartPortal/smartPortal.load#/portfolio/503

Para registro de erros ou sugestão de melhorias, acesse o endereço https://github.com/spbgovbr/mod-sei-incom/issues/new?issue%5Bassignee_id%5D=&issue%5Bmilestone_id%5D=
