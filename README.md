# SistemasOperacionais
Este respositório é dedicado para os meus códigos malucos feitos na cadeira de sistemas operacionais.

# Codigo C
Bibliotecas:
```c
#include <stdio.h>
#include <unistd.h>
#include <signal.h>
```
A função signal(SIGUSR1, handleSignal) define o sinal do usuário que mais tarde será enviado para o processo pai.

```c
signal(SIGUSR1, handleSignal);
```
A função handleSingal(int singalNumber) recebe um int e retorna a mensagem de que o sinal está sendo enviado.
```c
// Função de tratamento de sinal
void handleSignal(int signalNumber) {
    if (signalNumber == SIGUSR1) {
        printf("Enviando o sinal SIGUSR1\n");
        sleep(2);
    }
}
```
child_pid é uma variável que vai receber o identificador do processo atual.
```c
__pid_t child_pid;
```
A primeira parte do bloco condicional inicializa child_pid com o retorno da função fork(). 
Se pid igual a 0: então o processo sendo executado é o processo filho. O processo filho espera 5 segundos para enviar
o signal para o processo pai.
A função fork() inicializa um processo filho a partir do processo pai. O processo filho executa o mesmo
código que o processo pai escrito a partir do ponto em que fork() foi utilizada.
```c
if ((child_pid = fork()) == 0) {
        // Código executado pelo processo filho
        sleep(2);
        printf("Procesos filho %i\n", child_pid);
        sleep(2);
        printf("O filho irá esperar 5 segundos antes de enviar o sinal\n");
        sleep(5);
        kill(getppid(), SIGUSR1);
    }
```
Se pid maior que zero: então o processo sendo executado é o processo pai. Ele permanece pausado enquanto o processo filho espera os 5 segundos.
```c
else if (child_pid > 0) {
        // Código executado pelo processo pai
        printf("Processo pai %i\n", child_pid);
        sleep(2);
        printf("Processo pai esperando pelo sinal...\n");
        sleep(2);
        // O processo pai permanece em espera até receber um sinal
        pause();
        printf("Processo pai recebeu o sinal. Terminando.\n");
    }
```
Else: fork() retorne um valor abaixo de 0, será lançado um erro e a função main  acaba.
```c
else {
        // Tratamento de erro na criação do processo filho
        perror("Erro ao criar o processo filho");
        return 1;
    }
```