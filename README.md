# **Check Point 1 - Web Vulnerabilities & Exploits**

## <p style="text-align: center;"><img src="https://hipsters.jobs/files/pictures/logofiap.png"  width="100" height="100"></p>

## **<p style="text-align: center;">Matheus de Lima Sabo - RM92819 - 2TDCF</p>**
---

## Falhas de segurança que serão apresentadas
* Remote Code Execution (RCE)
* Path Traversal
* Information Leakage

## Para cada falha serão apresentados os seguintes tópicos
* Uma breve descrição
* Seu impacto em uma empresa e sua estrutura
* Uma referência para uma palestra ou artigo dedicado à falha
* Um report de uma campanha de bug bounty onde a falha é citada
* Um exemplo de código PHP vulnerável a falha em questão
* Uma exploração da falha a partir do mesmo código PHP citado anteriormente (script e prova de conceito)



# **Falhas de segurança:** <p style="text-align: center;">1. Remote Code Execution (RCE)</p>

Uma vulnerabilidade de _Remote Code Execution (RCE)_ é uma falha que permite que um atacante execute códigos arbitrários de forma remota. Os ataques RCE podem ser usados com diversos objetivos. Dentre eles estão:
* Acesso Inicial
    * Neste cenário, _RCE's_ geralmente são usados para a instalação de malwares de persistência para um ataque mais complexo
* Divulgação de Informações
    * Também é possível exfiltrar dados diretamente a partir de vulnerabilidades de RCE
* Negação de serviço
    * Ataques de RCE permitem que um invasor interrompa operações da aplicação vulnerável ou de outras aplicações do mesmo servidor
* Mineiração
    * Existem malwares dedicados especificamente à criptomineração. É possível utilizar da falha de RCE para instalá-los e utilizar dos recursos computacionais de terceiros para minerar criptomoedas.
* Ransomware
    * O ato de executar códigos remotamente no servidor também permite a instalação de ransomwares. Eles encriptam todos os dados do servidor e oferecem a recuperação desses dados em troca de uma quantia em criptomoedas



### **<p style="text-align: center;">Segue um vídeo no youtube que exemplifica um ataque que faz uso de RCE.</p>**

<p style="text-align: center;"><a href="http://www.youtube.com/watch?feature=player_embedded&v=3ErMkc07VNk" target="_blank">
 <img src="https://i.ytimg.com/vi/3ErMkc07VNk/maxresdefault.jpg" alt="ExploitDB - Apache HTTP Server 2.4.50 Remote Code Execution" width="480" height="270" border="1"/>
</a></p>



No ataque em questão, é executada uma _reverse shell_ (onde o comando estabelece uma conexão direta entre o servidor e o cliente na porta especificada) permitindo acesso livre ao servidor pelo usuário do serviço vulnerável



### **<p style="text-align: center;">Segue um exemplo de um _bugbounty_ que possui uma falha de segurança de RCE</p>**
### <p style="text-align: center;">[Remote Code Execution on www.semrush.com/my_reports on Logo upload](https://hackerone.com/reports/403417)</p>



### **<p style="text-align: center;">Este é um exemplo de um código PHP vulnerável a RCE</p>**
```
<?php
  $name = $_GET['host'];
  echo "<pre>";
  system("host " . $name);
  echo "</pre>";
?>
```
Este código recebe do usuário uma string no parâmetro host e a aplica diretamente como argumento em um comando no sistema operacional, sem tratamento.




### **<p style="text-align: center;">Uma simples forma de explorar essa falha é:</p>**

```
curl "http:/alvo.target/arquivo.php?host=[domínio qualquer];[comando]"
```



### **<p style="text-align: center;">Exploração na prática</p>**
<p style="text-align: center;"><img src="https://cdn.discordapp.com/attachments/847460307826638848/1097601913157062726/poc_RCE.gif"  width="960" height="530"></p>



# <p style="text-align: center;">2. Path Traversal</p>

Uma vulneravilidade de _Path Traversal_ é uma falha que permite que um atacante leia arquivos arbitrários do servidor. Em alguns casos, também é possível até mesmo escrever e substituir arquivos existentes. Dentre as influências dessa vulnerabilidade no servidor, encontram-se:
* Leitura de arquivos do servidor
    * Com _Path Traversal_, é possível ler arquivos internos da aplicação e do servidor. Isso significa que _source codes_, credenciais _back-end_, arquivos confidenciais e entre outros podem ser revelados.

* Negação de serviço
    * Caso o atacante consiga escrever e substituir arquivos já existentes do servidor, é possível alterar arquivos críticos para o funcionamento de diversos serviços, interrompendo seu funcionamento.

* Dibulgação de informações
    * Além de ler obter acesso ao arquivos, é possível, obviamente, exfiltrar todos os arquivos do servidor, causando um grande vazamento de dados.



### **<p style="text-align: center;">Segue um vídeo no youtube que exemplifica um ataque que faz uso de _Path Traversal_.</p>**

<p style="text-align: center;"><a href="http://www.youtube.com/watch?feature=player_embedded&v=G8wnZDP3-eo" target="_blank">
 <img src="https://i.ytimg.com/vi/G8wnZDP3-eo/maxresdefault.jpg" alt="Exploit-DB - D-Link DAP-1620 A1 v1.01 - Directory Traversal" width="480" height="270" border="1"/>
</a></p>



No exemplo acima, o ataque de _Path Traversal_ ocorre no campo `html_response_page` da aplicação. É possível especificar `../../../../../../../../../../../etc/passwd`. Isso é feito com o objetivo de "voltar" diversos diretórios no sistema e receber como resposta o arquivo /etc/passwd



### **<p style="text-align: center;">Segue um exemplo de um _bugbounty_ que possui uma falha de segurança de _Path Traversal_</p>**
### <p style="text-align: center;">[Path traversal on ████████](https://hackerone.com/reports/217344)</p>



### **<p style="text-align: center;">Este é um exemplo de um código PHP vulnerável a _Path Traversal_</p>**
```
<?php
$template = 'blue.php';
   $template = $_COOKIE['TEMPLATE'];
include ( "/home/users/phpguru/templates/" . $template );
?>
```
Este código é vulnerável a _Path Traversal_ no cookie chamado TEMPLATE. Quando explorado, a response terá o conteúdo do arquivo especificado



### **<p style="text-align: center;">Uma simples forma de explorar essa falha é:</p>**

```
curl -b "TEMPLATE=../../../../../../../../../[arquivo]" "http:/alvo.target/"
```



### **<p style="text-align: center;">Exploração na prática</p>**
<p style="text-align: center;"><img src="https://cdn.discordapp.com/attachments/847460307826638848/1097616950349266954/poc_pt.gif"  width="960" height="530"></p>



# <p style="text-align: center;">3. Information Leakage</p>

_Information Leakage_ se dá, simplesmente, quando os dados acabam em algum lugar onde não deveriam estar expostos. Muito geralmente, isso acontece devido a erros cometidos durante a configuração de um serviço e/ou aplicação. Existem inúmeros exemplos de _Information Leakage_. Alguns poucos deles são:
* Erros de configuração ou Vazamento acidental
    * Nesse caso, ocorre quando o responsável pela configuração do serviço e/ou aplicação falha em tratar erros corretamente ou não limita o acesso de uma informação específica somente para quem pode vê-la.
* Funcionário mal-intencionado
    * Existem diversos casos onde um funcionário descontente e/ou mal-intencionado se compromete a prejudicar a empresa em que trabalha vazando ativamente dados confidenciais que teve acesso. A exposição de informações confidenciais pode levar a problemas para a organização. Também podem levar a ações judiciais, danos à reputação da empresa e/ou perda de clientes e confiança.
* Comunicações eletrônicas com intenção maliciosa
    * Além dos exemplos acima, também é possível obter informações confidenciais através de malwares e ataques de engenharia social. Ambos com alta taxa de sucesso, não é necessário muito (na maioria das vezes apenas uma ligação telefônica ou um email falsificado) e já é possível inserir um malware dentro da organização



Como _Information Leakage_ é um conceito muito amplo, este documento se limitará apenas à noção de Erros de configuração/Vazamento acidental a fim de exemplificar o conceito de forma mais tangível e com mais precisão.



### **<p style="text-align: center;">Segue um vídeo no youtube que exemplifica o conceito de _Information Leakage_.</p>**

<p style="text-align: center;"><a href="http://www.youtube.com/watch?feature=player_embedded&v=cP3HuNsIi4g" target="_blank">
 <img src="https://i.ytimg.com/vi/cP3HuNsIi4g/maxresdefault.jpg" alt="Exploit-DB - D-Link DAP-1620 A1 v1.01 - Directory Traversal" width="480" height="270" border="1"/>
</a></p>


No exemplo acima, Tod Beardsley comenta sobre a grande gama de _Information Leakage_ em sistemas de votação. Um dos conceitos que Tod aborda por exemplo é o Google Dorks para demonstrar como muitas informações importantes para uma organização estão públicas para qualquer um da internet.



### **<p style="text-align: center;">Segue um exemplo de um _bugbounty_ que possui uma falha de segurança de _Path Traversal_</p>**
### <p style="text-align: center;">[critical information disclosure](https://hackerone.com/reports/1106505)</p>



### **<p style="text-align: center;">Este é um exemplo de um código vulnerável a _Information Leakage_</p>**
Para criarmos um ambiente vulnerável a _Information Leakeage_ basta iniciarmos um serviço do Apache e não realizar a configuração responsavel pela ocultação da versão da aplicação.

```
systemctl start apache2
```


### **<p style="text-align: center;">Uma simples forma de explorar essa falha é:</p>**

```
curl "http://alvo.target/[diretorio-inexistente]"
```
Isso é o suficiente para revelar diversas informações sobre a aplicação.



### **<p style="text-align: center;">Exploração na prática</p>**
<p style="text-align: center;"><img src="https://cdn.discordapp.com/attachments/847460307826638848/1097637940903034890/poc_infodisc.gif"  width="960" height="530"></p>

---

## Códigos Vulneráveis obtidos em:

* Remote Code Execution (RCE)
    * Aula ministrada por [Rafael (ChOkO) Trassi](https://www.linkedin.com/in/rafael-trassi/) na FIAP
* Path Traversal
    * [OWASP - Path Traversal](https://owasp.org/www-community/attacks/Path_Traversal) (exemplo 4)
* Information Leakage (não se trata de um código vulnerável, somente um serviço mal configurado)
    * Manual de instalação apache
