# Semana #10

## Secret Key Encryption

### Tarefa 1: Frequency Analysis

Para esta tarefa, é-nos dado um ficheiro `ciphertext.txt` cujo conteúdo se encontra encriptado com recurso a uma cifra monoalfabética - cifra na qual cada letra é substituída por outra letra e esta substituição não variará ao longo do texto. O nosso objetivo é encontrar o texto original através de uma análise de frequência.
Correndo o programa `freq.py`, obtemos a tabela de frequências das letras do texto encriptado:

```
1-gram (top 20):                2-gram (top 20):                3-gram (top 20):
    n: 488                          yt: 115                         ytn: 78
    y: 373                          tn: 89                          vup: 30
    v: 348                          mu: 74                          mur: 20
    x: 291                          nh: 58                          ynh: 18
    u: 280                          vh: 57                          xzy: 16
    q: 276                          hn: 57                          mxu: 14
    m: 264                          vu: 56                          gnq: 14
    h: 235                          nq: 53                          ytv: 13
    t: 183                          xu: 52                          nqy: 13
    i: 166                          up: 46                          vii: 13
    p: 156                          xh: 45                          bxh: 13
    a: 116                          yn: 44                          lvq: 12
    c: 104                          np: 44                          nuy: 12
    z: 95                           vy: 44                          vyn: 12
    l: 90                           nu: 42                          uvy: 11
    g: 83                           qy: 39                          lmu: 11
    b: 83                           vq: 33                          nvh: 11
    r: 82                           vi: 32                          cmu: 11
    e: 76                           gn: 32                          tmq: 10
    d: 59                           av: 31                          vhp: 10
```

Estas tabelas mostram as frequências de letras isoladas, sequências de duas letras e sequências de três letras (1-gram, 2-gram, 3-gram, respetivamente). Consultando os artigos [1-Gram](https://en.wikipedia.org/wiki/Frequency_analysis), [2-Gram](https://en.wikipedia.org/wiki/Bigram) e [3-Gram](https://en.wikipedia.org/wiki/Trigram) podemos verificar essas mesmas frequências de ocorrência na Lingua Inglesa.

Analisando os dados que obtivémos, podemos ver que as duas primeiras sequências do 2-Gram e 3-Gram coincidem com as duas sequências de 2-Gram e a sequência de 3-Gram mais comuns na lingua Inglesa, procedemos assim à seguinte substituição:

```bash
tr 'ytn' 'THE' < ciphertext.txt > dec.txt
```

A partir daqui é feita uma análise tanto do texto e das frequências de modo a fazer as substituições mais seguras possíveis. A partir de certo ponto, torna-se fácil perceber o texto original e fazer as substituições corretas por haver palavras às quais já só falta uma letra.
O comando que desencripta o ficheiro todo é o seguinte:

```bash
tr 'ytnmuvupxiqjzhgrdlabcsefko' 'THEINANDOLSQURBGYWCFMKPVXJ' < ciphertext.txt > dec.txt
```

### Tarefa 2: Encriptação com diferentes Cifras e Modos

Nesta tarefa devemos testar o uso do comando `openssl` para encriptar e desencriptar ficheiros com diferentes cifras e modos. Usàmos o ficheiro criado na tarefa anterior (`dec.txt`) para testar os diferentes modos e cifras.
Correndo o comando `man openssl` podemos verificar que o comando suporta as seguintes cifras:

```
AES-128 Cipher, AES-192 Cipher, AES-256 Cipher, Aria-128 Cipher, Aria-192 Cipher, Aria-256 Cipher, Base64 Encoding, Blowfish Cipher, Camellia-128 Cipher, Camellia-192 Cipher, Camellia-256 Cipher, CAST Cipher, CAST5 Cipher, Chacha20 Cipher, DES Cipher, Triple-DES Cipher, IDEA Cipher, RC2 Cipher, RC4 Cipher, RC5 Cipher, SEED Cipher, SM4 Cipher
```

Para a grande parte das cifras também é fornecido um modo de operação (`cbc`, `cfb`, `ctr`, `ecb`, `ofb`) que definirá como a cifra é aplicada, bloco a bloco.

Testamos os seguintes comandos:

```bash
12:19:24 : diogotvf7@diogotvf7 $ openssl enc -aes128 -e -in dec.txt -out cipher.bin -K 00112233445566778889aabbccddeeff -iv 0102030405060708
hex string is too short, padding with zero bytes to length

12:27:03 : diogotvf7@diogotvf7 $ openssl enc -aria-128-ctr -e -in dec.txt -out cipher.bin -K 00112233445566778889aabbccddeeff -iv 0102030405060708
hex string is too short, padding with zero bytes to length

12:28:03 : diogotvf7@diogotvf7 $ openssl enc -aria-128-ecb -e -in dec.txt -out cipher.bin -K 00112233445566778889aabbccddeeff
```

Obs.: O último método de encriptação não precisa de -iv por ser no modo `ecb` que encripta os blocos independentemente entre sí.

### Tarefa 3: ECB vs. CBC

| Nesta tarefa é-nos dada uma imagem que teremos de encriptar nos modos `ecb` e `cbc` para depois podermos comparar os resultados. | <img src="images/logbook10/pic_original.bmp"> |
| :------------------------------------------------------------------------------------------------------------------------------: | :-------------------------------------------: |
|                                                                                                                                  |                Imagem original                |

Foram por isso corridos os seguintes comandos:

```bash
openssl enc -aes-128-cbc -e -in pic_original.bmp -out pic_original_cbc.bmp -K 00112233445566778889aabbccddeeff -iv 0102030405060708
openssl enc -aes-128-ecb -e -in pic_original.bmp -out pic_original_ecb.bmp -K 00112233445566778889aabbccddeeff -iv 0102030405060708
```

sendo o seu output os ficheiros `pic_cbc.bmp` e `pic_ecb.bmp`.

Estes ficheiros, de momento, não conseguem ser abertos em nenhum visualizador de imagens uma vez que os seus headers estão encriptados. Copiamois então os headers da imagem original para os ficheiros encriptados:

```bash
head -c 54 pic_original.bmp > header
tail -c +55 pic_cbc.bmp > body
cat header body > pic_cbc.bmp
tail -c +55 pic_ecb.bmp > body
cat header body > pic_ecb.bmp
```

| <img src="images/logbook10/pic_ecb.bmp"> | <img src="images/logbook10/pic_cbc.bmp"> |
| :--------------------------------------: | :--------------------------------------: |
|     Imagem encriptada em modo `ecb`      |     Imagem encriptada em modo `cbc`      |

É possível analisar que a informação foi muito melhor escondida na modo cbc. Para perceber o motivo basta atentar ao modo de operação - simétrico e assimétrico, respetivamente - de cada um:

#### ECB: Electronic Code Book (simétrica)

Este é o modo de encriptação mais simples. A informação a encriptar é dividido em blocos de igual tamanho e cada um desses blocos será encriptado individualmente com a mesma chave. Isto verifica-se na imagem encriptada no modo `ecb` onde os seus bytes de informação são alvo de uma encriptação uniforme. Esta uniformidade faz com que, aind que com cores diferentes, seja possível perceber a forma da imagem original.
Outra das desvantafens deste modo de encriptação foi verificada na tarefa 1 onde o texto foi encriptado letra a letra. Com uma encriptação simétrica podemos mais facilmente inferir o seu conteúdo através da análise de frequência.

#### CBC: Cipher Block Chaining (assimétrica)

Este modo é mais complexo que o modo anterior. Igualmente ao processo anterior, a informação que se pretende encriptar é separada por blocos de igual tamanho Inicialmente, é fornecido um vetor inicial (do tamanho dos blocos) com o qual faremos uma operação XOR com o primeiro bloco de informação. Apenas depois será aplicada a chave de encriptação ao primeiro bloco. O valor resultante, para além de ser a informação encriptada, será usado para efetuar a operação XOR com o bloco seguinte. Este processo é repetido até se chegar ao bloco final. O resultado - como se pode verificar - será muito mais aleatório e difícil de encriptar uma vez que cada bloco será único - já não poderá ser feita a análise por frequências.
Uma vantagem deste modo reside no facto de, apesar da sua encriptação ter de ser feita sequencialmente, a sua desencriptação pode ser paralelizada dado que o primeiro vetor de inicialização ser público e os seguintes vetores de inicialização serem os blocos já encriptados. A paralelização durante a desencriptação permite uma desencriptação mais rápida.
