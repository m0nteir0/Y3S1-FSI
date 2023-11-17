# Format String - TO FINISH

## Desafio 1
É nos fornecido um ficheiro zip, com um executável e um código fonte. Ambos servem para tentar obter a 'dummy flag' localmente para depois usar o exploit na port 4004 do servidor ctf-fsi.fe.up.pt. Para testar o exploit localmente, basta correr o script 'exploit_example.py'.

Ao corrermos checksec no executável, obtemos o seguinte: <br>
<img src="images/ctf7/ctf7_p1.png"/>

A stack encontra-se protegida, o que significa que não podemos fazer buffer overflows. Existem proteções do endereço de retorno usando canários, porém o binário não é randomizado.

Analisando o código fonte, podemos ver que o programa usa 'scanf' para ler o input do utilizador, o que significa que podemos fazer um format string attack.

```c
scanf("%32s", &buffer);
```

Uma vez que a flag se encontra numa variável global e através do checksec percebemos que os endereços do programa são estáticos, podemos usar o gdb para obter o endereço da flag.

Ao corrermos o script 'exploit_example.py' localmente, obtemos o o pid do processo que está a correr o programa:
<img src="images/ctf7/ctf7_p2.png"/>


Isto vai-nos permitir dar attach ao processo com o gdb:<br>
<img src="images/ctf7/ctf7_p3.png">

Ao corrermos o programa no gdb, podemos ver que o endereço da flag é 0x0804c060:

<img src="images/ctf7/ctf7_p4.png"/>

### Solução

Para obter a flag, basta fazer um format string attack, escrevendo o endereço da flag no buffer. Para isso, basta correr o script 'exploit_example.py' localmente.

```python
p.recvuntil(b"got:")
p.sendline(b"\x60\xc0\x04\x08%s")
p.interactive()
```
Este input é apenas o adress da flag em little endian, seguido de %s para que o printf imprima o que está no endereço da flag.

Ao corrermos no servidor ctf-fsi.fe.up.pt, obtemos a flag:

<img src="images/ctf7/ctf7_p5.png"/>

## Desafio 2

É nos fornecido outro ficheiro zip com um executável, e o seu código fonte. A flag encontra-se novamente no ficheiro 'flag.txt'

Ao corrermos o checksec obtemos este output:

<img src="images/ctf7/ctf7_p6.png" />

O programa continua sem randomização de endereços. Desta vez o código fonte lança a bash se a variável 'key' for 0xBEEF, ou 48879 em decimal. Com esta backdoor podemos ter acesso total ao servidor e desta maneira conseguir o conteúdo da flag.

Como a variável 'key' é global, temos que ter acesso ao seu address. Para isso recorremos novamente ao gdb:

<img src="images/ctf7/ctf7_p7.png" />

### Solução

Para conseguirmos escrever no endereço da variável 'key', temos de utilizar o *format specifier* `%n`, para escrever no endereço especificado o número de bytes escritos até ao momento. Para acedermos à backdoor, temos de escrever 48879 bytes. Como os primeiros 4 bytes serão o endereço da váriável 'key', os restantes 48875 serão preenchidos com o *format specifier* `%x` com uma largura fixa.

<img src="images/ctf7/ctf7_p9.png" />

De seguida, corremos o script 'exploit.py' para e conseguimos assim acesso ao backdoor. Com o comando `cat flag.txt` conseguimos obter a flag.

<img src="images/ctf7/ctf7_p8.png" />







