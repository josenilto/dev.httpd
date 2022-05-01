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
httpd_use_cifs --> desligado
httpd_use_gpg --> desativado
httpd_use_nfs --> desligado
httpd_use_openstack --> desligado
httpd_verify_dns --> desativado

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

Os Hosts Virtuais permitem que vários sites sejam hospedados por uma única máquina física, com cada site sendo aparentemente independente um do outro. Os hosts virtuais podem ser baseados em IP, mas normalmente são baseados em nome, o que significa que o nome de domínio na URL usada para acessar o servidor da Web determina para qual host virtual a solicitação se destina.

✅ Crie os seguintes diretórios como locais para dois hosts virtuais. Também criei um arquivo de teste em ambas as raízes do documento.
