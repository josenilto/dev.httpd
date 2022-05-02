# 🛠 DEV HTTPD | Configuração do servidor HTTP Linux

✅ Este artigo descreve a instalação e configuração de um servidor HTTP no Linux, com referência específica às informações necessárias para o exame RHCE EX300.  

✅ Lembre-se, os exames são práticos, portanto, não importa qual método você use para obter o resultado, desde que o produto final esteja correto.


✅ Artigos relacionados.


## Instalação

✅ Para uma instalação mínima do servidor HTTP, emita o comando a seguir.

```bash
sudo apt install apache2        [No Debian/Ubuntu]
sudo yum install httpd          [No RHEL/Centos]
sudo dnf install httpd          [No Fedora 22+]
sudo zypper install apache2     [No openSUSE]
```

✅ Verificar a versão, depois de instalado, pode verificar a versão com um dos seguintes comandos.

```bash
sudo httpd -v                   [No RHEL/Centos]
sudo apache2 -v                 [No Debian/Ubuntu]
```

✅ Verificar se a configuração do Apache tem erros, para verificar se existem erros na configuração do servidor Apache, pode usar o seguinte comando.

```bash
sudo httpd -t                   [No RHEL/Centos]
sudo apache2ctl -t              [No Debian/Ubuntu]
```

✅ Se desejar uma instalação mais completa, você pode instalar o grupo de pacotes **`Servidor Web`**.

```bash
sudo yum groupinstall "Web Server"
```

✅ Certifique-se de que o arquivo **`/etc/hosts`** contenha referências para o endereço de **`loopback`** e o nome do host.

```bash
127.0.0.1      localhost localhost.localdomain localhost4 localhost4.localdomain4
192.168.122.89 rhce1.localdomain rhce1
```

✅ Ligue o servidor HTTP e certifique-se de que ele seja parar e iniciar automaticamente na reinicialização.

```bash
sudo service httpd stop
sudo service httpd start
```

✅ Ver lista completa de serviços usando chkconfig.

```bash
sudo chkconfig --list
```

✅ Veja a lista completa de serviços que iniciam na inicialização (normalmente, nível de execução 3).

```bash
sudo chkconfig --list | grep 3:on
```

Ativar um serviço para os níveis de execução padrão (2,3,4,5).

```bash
sudo chkconfig httpd on
```

✅ Desativar um serviço para os níveis de execução padrão (2,3,4,5).

```bash
sudo chkconfig httpd off
```

✅ Ativar um serviço para um nível de execução selecionado.

```bash
sudo chkconfig --level 3 httpd on
```

✅ Também é possível combinar vários níveis em um comando.

```bash
sudo chkconfig --level 35 httpd on
```

✅ Desativar um serviço para um nível de execução selecionado.

```bash
sudo chkconfig --level 3 httpd off
```

✅ Também é possível combinar vários níveis em um comando.

```bash
sudo chkconfig --level 35 httpd off
```

✅ O servidor HTTP agora está instalado e em execução.  
 
✅ Os arquivos de configuração HTTP estão localizados no diretório **`/etc/httpd`**, com o arquivo de configuração principal sendo o arquivo **`/etc/httpd/conf/httpd.conf`**. A raiz do documento padrão é **`/var/www/html`**.  

✅ Quaisquer arquivos ou diretórios abaixo desse ponto ficarão visíveis usando um navegador assim que você configurar o firewall.  

✅ As alterações no arquivo **`/etc/httpd/conf/httpd.conf`** devem ser seguidas por um recarregamento ou reinicialização do serviço httpd.

```bash
sudo service httpd reload
sudo service httpd restart
```

✅ Iniciar o serviço, para iniciar o servidor Web, deve usar um dos comandos, de acordo com a versão do Linux em uso.

```bash
-------- No CentOS/RHEL -------- 
sudo systemctl start httpd
sudo service httpd start
 
-------- No Ubuntu/Debian --------
sudo systemctl start apache2
sudo service apache2 start
```

✅ Ativar o Apache no arranque, para proceder à ativação do Apache no arranque, deve usar um dos seguintes comandos.

```bash
-------- No CentOS/RHEL -------- 
sudo systemctl enable httpd
sudo chkconfig httpd on
 
-------- No Ubuntu/Debian --------
sudo systemctl enable apache2
sudo chkconfig apache2 on
```

✅ Restart ao Apache, para reiniciar o Apache deve usar um dos seguintes comandos.

```bash
-------- No CentOS/RHEL --------
sudo systemctl restart httpd
sudo service httpd restart
 
-------- No Ubuntu/Debian --------
sudo systemctl restart apache2
sudo service apache2 restart
```

✅ Saber o estado do serviço Web Apache, para saber o estado (status) do serviço, usem um dos seguintes comandos.

```bash
-------- No CentOS/RHEL -------- 
sudo systemctl status httpd
sudo service httpd status
 
-------- No Ubuntu/Debian --------
sudo systemctl status apache2
sudo service apache2 status
```

✅ Parar o serviço Web Apache, para parar o serviço, usem um dos seguintes comandos.

```bash
-------- No CentOS/RHEL -------- 
sudo systemctl stop httpd
sudo service httpd stop
 
-------- No Ubuntu/Debian --------
sudo systemctl stop apache2
sudo service apache2 stop
```

✅ Outros comandos/parâmetros.

Para saber que outros parâmetros podem usar com o comando httpd e apache2 usem o parâmetro -h

```bash
sudo httpd -h 
sudo apache2 -h 

sudo systemctl -h apache2
```

## Firewall

✅ Se você estiver usando o firewall do Linux, precisará fazer um furo no firewall para a porta 80 (e 443 para HTTPS) para garantir que o servidor HTTP possa ser acessado a partir da rede. Existem várias maneiras de fazer isso:

✅ A caixa de diálogo **`Configuração de firewall`** no menu (Sistema > Administração > Firewall) ou iniciada a partir da linha de comando executando o system-config-firewallcomando. Na seção "Serviços confiáveis", role a lista e marque a opção **`WWW (HTTP)`** e clique no botão "Aplicar".

✅ O utilitário **`Configuração de firewall`** baseado em texto **`(system-config-firewall-tui)`**. Esta é a versão baseada em texto da caixa de diálogo acima.  

✅ Usando o iptables serviço diretamente, conforme descrito aqui . Nesse caso, poderíamos precisar da seguinte entrada.

```bash
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
```

✅ Abra a porta do firewall para httpd.
✅ Se você tiver um serviço firewalld em execução, permita a porta **`:80`** de acesso ao browser da rede:

```bash
sudo firewall-cmd --permanent --add-service=httpd

sudo firewall-cmd --reload
sudo firewall-cmd --list-all 
```

## SELinux

✅ Se você estiver usando o SELinux, precisará considerar os seguintes pontos.

✅ Os booleanos do SELinux associados ao serviço httpd são exibidos usando o getseboolcomando.

```bash
getsebool -a | grep httpd
```

```bash
allow_httpd_anon_write              --> off  
allow_httpd_mod_auth_ntlm_winbind   --> off  
allow_httpd_mod_auth_pam            --> desligado  
allow_httpd_sys_script_anon_write   --> off  

httpd_builtin_scripting             --> em  
httpd_can_check_spam                --> desativado  
httpd_can_network_connect           --> desligado  
httpd_can_network_connect_cobbler   --> desligado  
httpd_can_network_connect_db        --> desativado  
httpd_can_network_memcache          --> desativado  
httpd_can_network_relay             --> desligado  
httpd_can_sendmail                  --> desativado  
httpd_dbus_avahi                    --> em  
httpd_enable_cgi                    --> em  
httpd_enable_ftp_server             --> desligado  
httpd_enable_homedirs               --> desativado  
httpd_execmem                       --> desligado  
httpd_manage_ipa                    --> desligado  
httpd_read_user_content             --> desativado  
httpd_run_stickshift                --> desligado  
httpd_setrlimit                     --> desligado  
httpd_ssi_exec                      --> desligado  
httpd_tmp_exec                      --> desligado  
httpd_tty_comm                      --> em  
httpd_unified                       --> em  
httpd_use_cifs                      --> desligado  
httpd_use_gpg                       --> desativado  
httpd_use_nfs                       --> desligado  
httpd_use_openstack                 --> desligado  
httpd_verify_dns                    --> desativado
```

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
sudo mkdir -p /www/meusite1.com/logs
sudo mkdir -p /www/meusite1.com/html
echo "Arquivo de teste MySite1.com" > /www/mysite1.com/html/test.txt

sudo mkdir -p /www/mysite2.com/logs
sudo mkdir -p /www/meusite2.com/html
echo "Arquivo de teste do MySite2.com" > /www/mysite2.com/html/test.txt
```

✅ Se você estiver usando o **`SELinux`**, certifique-se de que os diretórios e seus conteúdos estejam atribuídos ao contexto correto.

```bash
semanage fcontext -a -t httpd_sys_content_t "/www(/.*)?"
restorecon -F -R -v /www
```

✅ Os hosts virtuais são definidos no arquivo **`/etc/httpd/conf/httpd.conf`**. A definição dos dois hosts virtuais é mostrada abaixo.

```bash
NameVirtualHost *:80

<VirtualHost *:80>  
    ServerName www.mysite1.com  
    Serveralias mysite1.com  
    DocumentRoot /www/mysite1.com/html  
    ErrorLog /www/mysite1.com/logs/mysite1.com-error_log
</VirtualHost>

<VirtualHost *:80>  
    ServerName www.mysite2.com  
    Serveralias mysite2.com  
    DocumentRoot /www/mysite2.com/html  
    ErrorLog /www/mysite2.com/logs/mysite2.com-error_log
</VirtualHost>
```

✅ Recarregue ou reinicie o serviço httpd para que as alterações tenham efeito.

```bash
sudo service httpd reload
sudo service httpd restart
```

✅ Desde que o DNS, ou arquivo hosts, resolva os nomes **`meusite1.com`** e **`meusite2.com`** para o endereço IP do servidor da Web, as páginas sob as raízes do documento agora serão exibidas para cada host virtual. Para testar isso, você pode alterar seu arquivo hosts com as seguintes entradas.

```bash
127.0.0.1 mysite1.com mysite1
127.0.0.1 mysite2.com mysite2
```

✅ Agora você deve ver a página de teste correta em cada um dos seguintes URLs no servidor web.

```bash
http://mysite1.com/test.txt
http://mysite2.com/test.txt
```

## Diretórios Privados

✅ Usando os hosts virtuais que criamos anteriormente, crie um novo diretório chamado **`private`** e coloque um arquivo nele.

```bash
sudo mkdir /www/mysite1.com/html/private
echo "MySite1.com Private Test file" > /www/mysite1.com/html/private/test.txt
```

✅ Crie um arquivo **`.htpasswd`** contendo um nome de **`usuário/senha`** e adicione uma segunda entrada.

```bash
cd /www/mysite1.com/html/private
htpasswd -cmb .htpasswd user1 password1
htpasswd -mb .htpasswd user2 password2
```

✅ Edite o arquivo **`/etc/httpd/conf/httpd.conf`** com uma entrada como a seguinte.

```bash
<Directory "/www/mysite1.com/html/private">  
    AuthType basic  
    AuthName "Private Access"  
    AuthUserFile "/www/mysite1.com/html/private/.htpasswd"  
    Require valid-user  
    Order allow,deny  
    Allow from all
</Directory>
```

✅ Recarregue ou reinicie o serviço httpd para que as alterações tenham efeito.

```bash
sudo service httpd reload
sudo service httpd restart
```

✅ Agora você deve ser solicitado a fornecer um nome de **`usuário/senha`** ao tentar acessar o arquivo a seguir.

```bash
http://mysite1.com/private/test.txt
```

## Conteúdo Gerenciado em Grupo

✅ Usando os hosts virtuais definidos anteriormente, habilitaremos o conteúdo gerenciado de grupo para **`mysite1.com`**.
✅ Crie um grupo do qual os usuários farão parte.

```bash
sudo groupadd webdevs
```

✅ Adicione os usuários necessários ao grupo.

```bash
# Create new users.
useradd -g webdevs user1
useradd -g webdevs user2

# Modify existing users.
usermod -g webdevs user1
usermod -g webdevs user2
```

✅ Altere a propriedade e as permissões dos diretórios que contêm o conteúdo gerenciado do grupo.

```bash
sudo chown -R apache.webdevs /www/mysite1.com/html
sudo chmod -R 775 /www/mysite1.com/html
sudo chmod -R g+s /www/mysite1.com/html
```

✅ Faça login nos dois usuários e verifique se eles podem adicionar e alterar o conteúdo.

```bash
su - user1
umask 002
echo "Test by user1" > /www/mysite1.com/html/group-test.txt
exit
logout

su - user2
umask 002
echo "Test by user2" >> /www/mysite1.com/html/group-test.txt
exit
logout
```

✅ O arquivo com o conteúdo de ambos os usuários é visível usando o seguinte URL.

```bash
http://mysite1.com/group-test.txt
```

✅ Observe a um ask configuração, que permite permissão de **`leitura/gravação`** para o grupo. Essa configuração pode ser colocada no arquivo **`~/.bashrc`** ou **`~/.bash_profile`** para cada usuário.

## Implantar um aplicativo CGI básico

✅ Crie um diretório chamado **`cgi-bin`** em um host virtual existente.

```bash
sudo mkdir /www/mysite2.com/html/gci-bin
```

✅ Crie um aplicativo CGI simples no diretório, por exemplo, um arquivo chamado **`helloworld.pl`** com o seguinte conteúdo.

```bash
#!/usr/bin/perl
print "Content-type: text/html\n\n";
print "helloWorld!";
```

✅ Altere a propriedade e verifique se o arquivo é executável.

```bash
sudo chown apache.apache helloworld.pl
sudo chmod u+x helloworld.pl
```

✅ Edite o arquivo **`/etc/httpd/conf/httpd.conf`**, incluindo as seguintes entradas na definição do host virtual.

```bash
ScriptAlias /cgi-bin/ /www/mysite2.com/html/gci-bin/  
Options +ExecCGI  
AddHandler cgi-script .pl .cgi
```

✅ Então a definição completa fica assim.

```bash
<VirtualHost *:80>  
    ServerName www.mysite2.com  
    Serveralias mysite2.com  
    DocumentRoot /www/mysite2.com/html  
    ErrorLog /www/mysite2.com/logs/mysite2.com-error_log  
    
    # Below added to support CGI applications  
    ScriptAlias /cgi-bin/ /www/mysite2.com/html/gci-bin/  
    Options +ExecCGI  
    AddHandler cgi-script .pl .cgi
</VirtualHost>
```

✅ Recarregue ou reinicie o serviço httpd para que as alterações tenham efeito.

```bash
sudo service httpd reload
sudo service httpd restart
```

✅ O aplicativo CGI agora pode ser executado na seguinte URL.

```bash
http://mysite2.com/cgi-bin/helloworld.pl
```

Se você preferir que o diretório "cgi-bin" seja colocado em um local diferente, basta alterar a entrada "ScriptAlias" para refletir o local alterado.


## Configuração SSL (HTTPS)

✅ A configuração HTTPS não é um requisito do exame RHCE, mas é útil saber, então eu a incluí.  
✅ Se eles ainda não estiverem instalados, instale os pacotes **`mod_ssl`** e **`crypto-utils`**.

```bash
sudo yum install mod_ssl openssl crypto-utils
```

✅ A instalação do pacote **`mod_ssl`**, cria o arquivo de configuração **`/etc/httpd/conf.d/ssl.conf`**, que inclui referências ao certificado e à chave localhost autoassinado padrão. Isso é suficiente para testar a configuração SSL.  

✅ O serviço httpd deve ser reiniciado para que o módulo seja carregado, mas faremos isso mais tarde.  
✅ O genkey comando pode gerar uma solicitação de certificado ou um novo certificado autoassinado.  
✅ Para este teste, criei um novo certificado autoassinado.  

✅ Lembre-se, se você criptografar o certificado com uma senha, precisará digitá-la toda vez que iniciar o servidor HTTP.

```bash
genkey --makeca rhce1.localdomain
```

✅ Mova a chave e o certificado para os diretórios relevantes.

```bash
sudo mv /etc/pki/CA/private/rhce1.localdomain /etc/pki/tls/private/rhce1.localdomain
sudo mv /etc/pki/CA/rhce1.localdomain /etc/pki/tls/certs/rhce1.localdomain
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

✅ Observe que a entrada **`SSLCACertificateFile`** está comentada.  
✅ Se você estiver usando um certificado real, provavelmente precisará baixar o pacote intermediário da CA e referenciá-lo usando essa tag.  

✅ Reinicie o servidor HTTP.

```bash
sudo service httpd restart
```

✅ Desde que você tenha as configurações de firewall corretas, agora você poderá acessar seus aplicativos usando HTTPS.

```bash
https://rhce1.localdomain
```
