# Semana #3: CTF Wordpress CVE

## Tarefas

### Tarefa 1
> Ao navegar pela aplicação web, conseguimos encontrar informação pertinente para explorar vulnerabilidades, nomeadamente:
> - A versão do wordpress: **5.8.1**
> - A versão de 2 plugins utilizados: **5.7.1** do *WooCommerce* e **5.4.3**  do *Booster for WooCommerce*
> - **Orval Sanford** como administrador do website. <br>


### Tarefa 2
> A partir de pesquisas em várias bases de dados de CVEs, fomos procurando por uma vulnerabilidade que atingisse a versão do wordpress detetada, assim como as versões dos plugins mencionados anteriormente. Ao mesmo tempo, esta vulnerabiliade deveria possibilitar a autenticação como administrador.


### Tarefa 3
> Durante a pesquisa e análise de vários CVEs no site [CVEdetails](https://www.cvedetails.com/) relativos aos plugins de WordPress - e as suas respetivas versões - usados neste website co CTF, conseguimos encontrar o CVE:
> - **CVE-2021-34646, Critical Authentication Bypass Vulnerability Patched in Booster for WooCommerce**
>
> As informações relacionadas com este CVE mencionavam a possibilidade do atacante iniciar sessão como administrador, desde que certas opções do plugin estivessem ativas. <br>


### Tarefa 4
> Para procurar um exploit que tirasse partido deste vulnerabilidade, utilizamos os seguintes sites:
> - [CVEdetails](https://www.cvedetails.com/)
> - [ExploitDB](https://www.exploit-db.com/exploits/50299)
> - [Wordfence](https://www.wordfence.com/blog/2021/08/)
>
> Ao navegar no website rapidamente reparámos na existência do utilizador 'admin' que seria necessário para proceder ao exploit desta vulnerabilidade.
> Reunimos informação suficiente para explorar a vulnerabilidade, que descreveu os passos apresentados na secção seguinte. 


### Tarefa 5
> Para explorar a vulnerabilidade, fizemos os seguintes passos:
> - Enviámos um request para o URL da página inicial (home) do website, com o parâmetro wcj_user_id definido como 1 (ID do utilizador administrador). Este parâmetro define o ID do utilizador que o atacante quer fazer-se passar. Normalmente, a conta de administrador tem o ID 1.
> ````http
> http://ctf-fsi.fe.up.pt:5001/?wcj_user_id=1
> ````
> - Ao receber o request, a função reset_and_mail_activation_link() é acionada. Esta função recupera o user_id fornecido e gera um código posteriormente enviado por email para o utilizador o verificar.
> - A vulnerabilidade reside no facto de que o código de verificação é simplesmente um hash MD5 do tempo do pedido. Isto permite que um atacante recrie um código de verificação válido com base no tempo em que enviou um pedido de verificação para qualquer utilizador.
> - O parâmetro wcj_verify_email é definido como um objeto JSON codificado em JSON base64 que contém o ID do utilizador alvo (id) e o hash MD5 gerado (code) do tempo do pedido de verificação de email. Criámos um URL para verificação de email, usando o URL da página inicial do site. Obtivemos a data do envio do request ao observar os campos disponibilizados com a opção "Inspecionar" na página do website e usámos um [conversor online](https://www.epochconverter.com/) para converter *human-readable date* para *Epoch & Unix Timestamp*. De seguida, usamos um [gerador de hashes MD5](https://www.md5hashgenerator.com/) para codificar este *timestamp*. Por fim, utilizamos um [conversor de JSON para base64](https://codebeautify.org/json-to-base64-converter)  para colocar os parametros desejados no URL do website de modo a efetuar o pedido pretendido, por exemplo: 
> ````http
> http://ctf-fsi.fe.up.pt:5001/?wcj_verify_email=ewogICAgImlkIjogMSwKICAgICJjb2RlIjogImRmNzNhYTFiMmJmODk1MDhhMzdlMDc5NWQ0YjBmZWRmIgp9Cg==
> ````
> - Se a opção wcj_emails_verification_redirect_on_success estiver definida como 'yes' e os dados enviados no parâmetro wcj_verify_email forem válidos, certas funções (wp_set_current_user e wp_set_auth_cookie) são executadas. Isto gera uma sessão autenticada como o utilizador alvo, efetivamente contornando a autenticação e concedendo acesso à conta alvo. Estas opções estão definidas como 'yes' por *default*.
> - Com a exploração bem sucedida, ganhámos acesso administrativo ao site. Acedemos ao [link](http://ctf-fsi.fe.up.pt:5001/wp-admin/edit.php) do enunciado e rapidamente nos deparámos com a flag presente num post privado. <br>
