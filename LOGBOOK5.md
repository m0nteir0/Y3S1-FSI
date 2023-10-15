# Semana #5

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

> Compilámos o ficheiro call_shellcode.c e gerou ficheiros binários: `a64.out` e `a32.out.` <br> > <img src="image.png">
> Corremos ambos e o resultado foi semelhante - Abrimos uma shell no diretório onde o programa foi executado.

> //TODO -> N FUNCIONA C O EXECSTACK
> ![Alt text](image-1.png)

> <img src="i">

> <img src="i">

### Tarefa 2

> No código fornecido, verificamos a presença da vulnerabilidade referida, **buffer overflow**. Tal aconteceu porque se tentou copiar um array de caracteres com possibilidade de ter até 517 bytes (`char str[517]`)para um buffer de tamanho máximo de 100 bytes (`char buffer[BUF_SIZE]`). O `strcpy` não verifica os limites do tamanho do buffer e, assim, ocorrerá overflow.
> De seguida, foi pedido que desativassemos o StackGuard e o Non-Executable Stack. Para tal, compilámos o código com as flags `-fno-stack-protector` e `-z execstack` respetivamente. Apos compilar, alteramos o _ownership_ do programa para root e alteramos as permissões para 4755 para que o programa seja Set-UID.


### Tarefa 3

> Nesta tarefa, criamos o ficheiro `badfile`


### Task 4
