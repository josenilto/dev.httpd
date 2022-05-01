# 🛠 DEV HTTPD | Configuração do servidor HTTP Linux

Este artigo descreve a instalação e configuração de um servidor HTTP no Linux, com referência específica às informações necessárias para o exame RHCE EX300.  

Lembre-se, os exames são práticos, portanto, não importa qual método você use para obter o resultado, desde que o produto final esteja correto.

Artigos relacionados.


## Instalação

✅ Para uma instalação mínima do servidor HTTP, emita o comando a seguir.

```bash
yum install httpd
```

✅ Se desejar uma instalação mais completa, você pode instalar o grupo de pacotes **`Servidor Web`**.

```bash
yum groupinstall "Web Server"
```

✅ Certifique-se de que o arquivo **`/etc/hosts`** contenha referências para o endereço de loopback e o nome do host.

```bash
127.0.0.1      localhost localhost.localdomain localhost4 localhost4.localdomain4
192.168.122.89 rhce1.localdomain rhce1
```

✅ Ligue o servidor HTTP e certifique-se de que ele seja iniciado automaticamente na reinicialização.

```bash
service httpd start
chkconfig httpd on
```

O servidor HTTP agora está instalado e em execução.  
Os arquivos de configuração HTTP estão localizados no diretório **`/etc/httpd`**, com o arquivo de configuração principal sendo o arquivo **`/etc/httpd/conf/httpd.conf`**. A raiz do documento padrão é **`/var/www/html`**.  
Quaisquer arquivos ou diretórios abaixo desse ponto ficarão visíveis usando um navegador assim que você configurar o firewall.

As alterações no arquivo **`/etc/httpd/conf/httpd.conf`** devem ser seguidas por um recarregamento ou reinicialização do serviço httpd.

## Firewall

Se você estiver usando o firewall do Linux, precisará fazer um furo no firewall para a porta 80 (e 443 para HTTPS) para garantir que o servidor HTTP possa ser acessado a partir da rede. Existem várias maneiras de fazer isso:

A caixa de diálogo **`Configuração de firewall`** no menu (Sistema > Administração > Firewall) ou iniciada a partir da linha de comando executando o system-config-firewallcomando. Na seção "Serviços confiáveis", role a lista e marque a opção **`WWW (HTTP)`** e clique no botão "Aplicar".

✅ O utilitário **`Configuração de firewall`** baseado em texto **`(system-config-firewall-tui)`**.  

✅ Esta é a versão baseada em texto da caixa de diálogo acima.

✅ Usando o iptablesserviço diretamente, conforme descrito aqui . Nesse caso, poderíamos precisar da seguinte entrada.

```bash
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```

## SELinux

✅ Se você estiver usando o SELinux, precisará considerar os seguintes pontos.

✅ Os booleanos do SELinux associados ao serviço httpd são exibidos usando o getseboolcomando.

```bash
getsebool -a | grep httpd
```

allow_httpd_anon_write --> off  
allow_httpd_mod_auth_ntlm_winbind --> off  
allow_httpd_mod_auth_pam --> desligado  
allow_httpd_sys_script_anon_write --> off  

httpd_builtin_scripting --> em  
httpd_can_check_spam --> desativado  
httpd_can_network_connect --> desligado  
httpd_can_network_connect_cobbler --> desligado  
httpd_can_network_connect_db --> desativado  
httpd_can_network_memcache --> desativado  
httpd_can_network_relay --> desligado  
httpd_can_sendmail --> desativado  
httpd_dbus_avahi --> em  
httpd_enable_cgi --> em  
httpd_enable_ftp_server --> desligado  
httpd_enable_homedirs --> desativado  
httpd_execmem --> desligado  
httpd_manage_ipa --> desligado  
httpd_read_user_content --> desativado  
httpd_run_stickshift --> desligado  
httpd_setrlimit --> desligado  
httpd_ssi_exec --> desligado  
httpd_tmp_exec --> desligado  
httpd_tty_comm --> em  
httpd_unified --> em  
httpd_use_cifs          --> desligado  
httpd_use_gpg           --> desativado  
httpd_use_nfs           --> desligado  
httpd_use_openstack     --> desligado  
httpd_verify_dns        --> desativado

✅ O setseboolcomando é usado para definir um valor booleano específico.

```bash
setsebool httpd_use_nfs on
setsebool httpd_use_nfs off
```

✅ O httpd_sys_content_tcontexto deve ser atribuído a todo o conteúdo.

```bash
semanage fcontext -a -t httpd_sys_content_t "/var/www/html(/.*)?"
restorecon -F -R -v /var/www/html
```

✅ Você pode verificar a configuração de contexto atual em arquivos e diretórios usando o comando "ls -alZ".  

✅ Mais informações sobre o SELinux podem ser encontradas aqui .

## Hosts virtuais

✅ Os Hosts Virtuais permitem que vários sites sejam hospedados por uma única máquina física, com cada site sendo aparentemente independente um do outro.  

✅ Os hosts virtuais podem ser baseados em IP, mas normalmente são baseados em nome, o que significa que o nome de domínio na URL usada para acessar o servidor da Web determina para qual host virtual a solicitação se destina.

✅ Crie os seguintes diretórios como locais para dois hosts virtuais. Também criei um arquivo de teste em ambas as raízes do documento.

```bash
mkdir -p /www/meusite1.com/logs
mkdir -p /www/meusite1.com/html
echo "Arquivo de teste MySite1.com" > /www/mysite1.com/html/test.txt
mkdir -p /www/mysite2.com/logs
mkdir -p /www/meusite2.com/html
echo "Arquivo de teste do MySite2.com" > /www/mysite2.com/html/test.txt
```

✅ Se você estiver usando o **`SELinux`**, certifique-se de que os diretórios e seus conteúdos estejam atribuídos ao contexto correto.

```bash
semanage fcontext -a -t httpd_sys_content_t "/www(/.*)?"
restorecon -F -R -v /www
```

✅ Os hosts virtuais são definidos no arquivo **`/etc/httpd/conf/httpd.conf`**. A definição dos dois hosts virtuais é mostrada abaixo.


NomeVirtualHost *:80

<VirtualHost *:80>
    Nome do servidor www.meusite1.com
    Serveralias mysite1.com
    DocumentRoot /www/mysite1.com/html
    ErrorLog /www/mysite1.com/logs/mysite1.com-error_log
</VirtualHost>

<VirtualHost *:80>
    Nome do servidor www.meusite2.com
    Serveralias mysite2.com
    DocumentRoot /www/mysite2.com/html
    ErrorLog /www/mysite2.com/logs/mysite2.com-error_log
</VirtualHost>


✅ Recarregue ou reinicie o serviço httpd para que as alterações tenham efeito.


Desde que o DNS, ou arquivo hosts, resolva os nomes "meusite1.com" e "meusite2.com" para o endereço IP do servidor da Web, as páginas sob as raízes do documento agora serão exibidas para cada host virtual. Para testar isso, você pode alterar seu arquivo hosts com as seguintes entradas.


Agora você deve ver a página de teste correta em cada um dos seguintes URLs no servidor web.


## Diretórios Privados

✅ Usando os hosts virtuais que criamos anteriormente, crie um novo diretório chamado "private" e coloque um arquivo nele.



✅ Crie um arquivo ".htpasswd" contendo um nome de usuário/senha e adicione uma segunda entrada.


Edite o arquivo "/etc/httpd/conf/httpd.conf" com uma entrada como a seguinte.

Recarregue ou reinicie o serviço httpd para que as alterações tenham efeito.

Agora você deve ser solicitado a fornecer um nome de usuário/senha ao tentar acessar o arquivo a seguir.

## Conteúdo Gerenciado em Grupo

✅ Usando os hosts virtuais definidos anteriormente, habilitaremos o conteúdo gerenciado de grupo para **`mysite1.com`**.

✅ Crie um grupo do qual os usuários farão parte.

```bash
grupoadicionar webdevs
```

✅ Adicione os usuários necessários ao grupo.


✅ Altere a propriedade e as permissões dos diretórios que contêm o conteúdo gerenciado do grupo.

✅ Faça login nos dois usuários e verifique se eles podem adicionar e alterar o conteúdo.

✅ O arquivo com o conteúdo de ambos os usuários é visível usando o seguinte URL.

✅ Observe a um ask configuração, que permite permissão de **`leitura/gravação`** para o grupo. Essa configuração pode ser colocada no arquivo **`~/.bashrc`** ou **`~/.bash_profile`** para cada usuário.

## Implantar um aplicativo CGI básico

✅ Crie um diretório chamado **`cgi-bin`** em um host virtual existente.

✅ Crie um aplicativo CGI simples no diretório, por exemplo, um arquivo chamado **`helloworld.pl`** com o seguinte conteúdo.

✅ Altere a propriedade e verifique se o arquivo é executável.

✅ Edite o arquivo **`/etc/httpd/conf/httpd.conf`**, incluindo as seguintes entradas na definição do host virtual.

✅ Então a definição completa fica assim.

✅ Recarregue ou reinicie o serviço httpd para que as alterações tenham efeito.

✅ O aplicativo CGI agora pode ser executado na seguinte URL.

Se você preferir que o diretório "cgi-bin" seja colocado em um local diferente, basta alterar a entrada "ScriptAlias" para refletir o local alterado.


## Configuração SSL (HTTPS)

✅ A configuração HTTPS não é um requisito do exame RHCE, mas é útil saber, então eu a incluí.
✅ Se eles ainda não estiverem instalados, instale os mod_sslpacotes e openssl.crypto-utils

```bash
yum install mod_ssl openssl crypto-utils
```

✅ A instalação do mod_sslpacote cria o arquivo de configuração "/etc/httpd/conf.d/ssl.conf", que inclui referências ao certificado e à chave localhost autoassinado padrão. Isso é suficiente para testar a configuração SSL. O serviço httpd deve ser reiniciado para que o módulo seja carregado, mas faremos isso mais tarde.

✅ O genkeycomando pode gerar uma solicitação de certificado ou um novo certificado autoassinado. Para este teste, criei um novo certificado autoassinado. Lembre-se, se você criptografar o certificado com uma senha, precisará digitá-la toda vez que iniciar o servidor HTTP.

```bash
genkey --makeca rhce1.localdomain
```

✅ Mova a chave e o certificado para os diretórios relevantes.

```bash
mv /etc/pki/CA/private/rhce1.localdomain /etc/pki/tls/private/rhce1.localdomain
mv /etc/pki/CA/rhce1.localdomain /etc/pki/tls/certs/rhce1.localdomain
```

✅ Adicione/modifique as seguintes linhas no arquivo **`/etc/httpd/conf.d/ssl.conf`**.

```bash
SSLProtocol ALL -SSLv2 -SSLv3  
SSLHonorCipherOrder On  
SSLCipherSuite HIGH:!aNULL:!MD5:!3DES:!DES:!DHE:!RSA

SSLCertificateFile /etc/pki/tls/certs/rhce1.localdomain  
SSLCertificateKeyFile /etc/pki/tls/private/rhce1.localdomain  
SSLCACertificateFile /etc/pki/tls/certs/intermediate.crt
```

✅ Observe que a entrada "SSLCACertificateFile" está comentada.  
✅ Se você estiver usando um certificado real, provavelmente precisará baixar o pacote intermediário da CA e referenciá-lo usando essa tag.  
✅ Reinicie o servidor HTTP.

```bash
service httpd restart
```

✅ Desde que você tenha as configurações de firewall corretas, agora você poderá acessar seus aplicativos usando HTTPS.

```bash
https://rhce1.localdomain
```
