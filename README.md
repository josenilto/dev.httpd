# 🛠 DEV HTTPD | Configuração do servidor HTTP Linux

Este artigo descreve a instalação e configuração de um servidor HTTP no Linux, com referência específica às informações necessárias para o exame RHCE EX300 .

Lembre-se, os exames são práticos, portanto, não importa qual método você use para obter o resultado, desde que o produto final esteja correto.


Artigos relacionados.


## Instalação

Para uma instalação mínima do servidor HTTP, emita o comando a seguir.

``bash
yum install httpd
´´

Se desejar uma instalação mais completa, você pode instalar o grupo de pacotes "Servidor Web".


Certifique-se de que o arquivo "/etc/hosts" contenha referências para o endereço de loopback e o nome do host.




Ligue o servidor HTTP e certifique-se de que ele seja iniciado automaticamente na reinicialização.



httpd
