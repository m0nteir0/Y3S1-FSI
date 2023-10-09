## Tarefa 0: Environment Setup
> Como setup, tivemos que desligar algumas proteções do sistema operativo, para que fosse possível realizar as tarefas iniciais. 
Neste caso, desligámos a randomização do espaço de endereços e ligámos o `/bin/sh` ao `/bin/zsh` para podermos executar programas Set-UID sem as limitações impostas por *default*.
Para tal corremos os seguintes comandos:<br>
> **Address Space randomization** ->:
>```bash
> $ sudo sysctl -w kernel.randomize_va_space=0
>```
> **Configuring /bin/sh**:
> ```bash	
> $ sudo ln -sf /bin/zsh /bin/sh
>```
>Adicionalmente, há mais proteções do sistema operativo (**StackGuard** e **Non-Executable Stack**) que serão desativadas durante a compilação.

## Tarefa 1: Getting familiar with Shellcode
> Compilámos o ficheiro call_shellcode.c e gerou ficheiros binários: `a64.out` e `a32.out.` <br>
> <img>
> Corremos ambos e o resultado foi semelhante - Abrimos uma shell no diretório onde o programa foi executado. <br>
> <img>
> <img>


## Task 2


## Task 3


## Task 4

