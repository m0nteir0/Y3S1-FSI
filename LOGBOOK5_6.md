# Semana #5 e #6

## Buffer-Overflow Attack Lab: Tarefas

### Tarefa 0

> Como setup, tivemos que desligar algumas proteções do sistema operativo, para que fosse possível realizar as tarefas iniciais.
> Neste caso, desligámos a randomização do espaço de endereços e ligámos o `/bin/sh` ao `/bin/zsh` para podermos executar programas Set-UID sem as limitações impostas por _default_.
> Para tal corremos os seguintes comandos:<br> > **Address Space randomization** ->:
>
> ```bash
> $ sudo sysctl -w kernel.randomize_va_space=0
> ```
>
> **Configuring /bin/sh**:
>
> ```bash
> $ sudo ln -sf /bin/zsh /bin/sh
> ```
>
> Adicionalmente, há mais proteções do sistema operativo (**StackGuard** e **Non-Executable Stack**) que serão desativadas durante a compilação.

### Tarefa 1

> Compilámos o ficheiro call_shellcode.c e gerou ficheiros binários: `a64.out` e `a32.out.`
>
> <img src="images/logbook5/t1_1.png">
> Corremos ambos e o resultado foi semelhante - Abrimos uma shell no diretório onde o programa foi executado.
>
> ![Alt text](images/image-1.png)


### Tarefa 2

> No código fornecido, verificamos a presença da vulnerabilidade referida, **buffer overflow**. Tal aconteceu porque se tentou copiar um array de caracteres com possibilidade de ter até 517 bytes (`char str[517]`)para um buffer de tamanho máximo de 100 bytes (`char buffer[BUF_SIZE]`). O `strcpy` não verifica os limites do tamanho do buffer e, assim, ocorrerá overflow.
> De seguida, foi pedido que desativassemos o StackGuard e o Non-Executable Stack. Para tal, compilámos o código com as flags `-fno-stack-protector` e `-z execstack` respetivamente. Apos compilar, alteramos o _ownership_ do programa para root e alteramos as permissões para 4755 para que o programa seja Set-UID.

### Tarefa 3

> Nesta tarefa, criamos o ficheiro `badfile` inicialmente vazio. De seguida, corremos o código em modo debug e descobrimos o endereço de retorno da função `bof()` relativamente ao início do buffer, apos colocar um breakpoint na função `bof()` usando o debugger (`gdb`). Também obtivemos o valor do endereço do início do buffer. <br>
> <img src="/images/logbook5/log5t3_p1.png"> <br>
> Com os dois endereços obtidos, colocamos na variavel `shellcode` o _shellcode 32-bits_. Criamos com array de bytes com o _length_ máximo lido pelo ficheiro do porgrama `stack.c` (517), com todos os bytes inicializados como `NOP` (`0x90`).
>
> ```python
> # Fill the content with NOP's
> content = bytearray(0x90 for i in range(517))
> ```
>
> Alteramos a variavel start com o valor `517-len(shellcode)` e colocamos a `shellcode` no final do array content(desde `start` até `start+len(shellcode)`).
> O novo endereço de retorno também foi calculado com base no endereço do ebp (frame pointer) e no valor registado em `start`, de modo a que este fique a apontar para o início do _shellcode_.
>
> ```python
>  ret = 0xffffc438 + start
> ```
>
> Por fim, o `offset` obteve-se a partir da diferença entre o endereço de retorno e o endereço do início do buffer.
>
> ```python
> offset = 0xffffc438 - 0xffffc3cc + 4
> ```
>
> Este offset permite-no saber qual é o endereço do retorno, para o conseguirmo substituir pelo novo endereço de retorno calculado anteriormente, que apontará para o _shellcode_.
>
> ```python
> L = 4  # Use 4 for 32-bit address and 8 for 64-bit address
> content[offset : offset + L] = (ret).to_bytes(L, byteorder="little")
> ```
>
> Por fim, escrevemos o conteúdo do array `content` no ficheiro `badfile`.
>
>![Alt text](images/logbook5/log5t3_pt1.png)

### Task 4

> Nesta task temos um obstáculo, não fazer o ataque sabendo o tamanho real do buffer uma vez que em circunstâncias normais, este valor pode ser muito difícil de obter. Deste modo, o objetivo deste ataque é conseguir conseguir uma shell apenas sabendo que o tamanho do buffer está entre 100-200 bytes. Para tal, tivemos de alterar ligeiramente o nosso ficheiro `exploit.py`.
> Em vez de apenas escrevermos o endereço no endereço do return vamos escrever em vários endereços (todos estes múltiplos de 4 devido ao alinhamento da memória) começando 100 bytes após o início do buffer.   
> 
> ```python
> # Decide the return address value
> # and put it somewhere in the payload
> ret = 0xffffc7ac + start   # Change this number
> offset = 100  #0xffffc84c - 0xffffc7e0 + 4 # Change this number
>
> for i in range(0, 100, 4):
>    L = 4  # Use 4 for 32-bit address and 8 for 64-bit address
>    content[offset + i : offset + i + L] = (ret).to_bytes(L, byteorder="little")
>```
>
> Isto fará com que a chance de o return apontar para o nosso _shellcode_ seja muito maior. Por fim, corremos o programa e obtivemos uma shell com acesso root. 
>
>![Alt text](images/logbook5/log5t3_pt2.png)
