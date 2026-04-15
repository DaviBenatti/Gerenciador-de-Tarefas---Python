# 📝 Gerenciador de Tarefas CLI - Python

Um gerenciador de tarefas intuitivo para terminal.

## 🚀 Funcionalidades
- **CRUD Completo:** Adicionar, Listar, Concluir e Excluir tarefas.
- **Persistência de Dados:** Salva automaticamente em um arquivo `tarefas.json`.
- **Interface Adaptativa:** As linhas e separadores se ajustam ao tamanho do texto.
- **Robustez:** Tratamento de erros para entradas inválidas (letras em vez de números).
- **Visual:** Uso de cores ANSI e emojis para melhor experiência do usuário (UX).

## 🛠️ Tecnologias
- **Python 3.10+**
- **JSON** (Manipulação de arquivos)
- **OS** (Integração com o sistema operacional)

## 📋 Como rodar
1. Certifique-se de ter o Python instalado.
2. Clone o repositório ou baixe o arquivo `.py`.
3. No terminal, execute:
   ```bash
   python nome_do_arquivo.py

   Código:

import json
import os

VERDE = '\033[92m'
AMARELO = '\033[93m'
VERMELHO = '\033[91m'
RESET = '\033[0m'

def limpar_tela():
    os.system('cls' if os.name == 'nt' else 'clear')

def salvar_dados(lista):
    with open("tarefas.json", "w") as arquivo:
        json.dump(lista, arquivo, indent=4)

def carregar_dados():
    try:
        with open("tarefas.json", "r") as arquivo:
            return json.load(arquivo)
    except FileNotFoundError:
        return []

def ler_inteiro(mensagem: str) -> int:
    while True:
        try:
            return int(input(mensagem))
        except ValueError:
            print(f"\n{VERMELHO}ERRO: Digite um número inteiro válido!{RESET}")

def obter_largura(lista: list, minimo: int = 40, texto: str = "") -> int:
    tamanho_texto = len(texto)
    tamanho_maior_tarefa = 0
    if lista:
        tamanho_maior_tarefa = max(len(tarefa['item']) for tarefa in lista)
    largura_lista = tamanho_maior_tarefa + 22

    return max(minimo, tamanho_texto + 4, largura_lista)

def imprimir_linha(lista: list, texto: str = ""):
    largura = obter_largura(lista, texto=texto)
    print("-" * largura)

def menu(lista):
    largura = obter_largura(lista)
    imprimir_linha(lista)
    print(f"{VERDE}BEM-VINDO AO GERENCIADOR DE TAREFAS!{RESET}".center(largura + 10))
    imprimir_linha(lista)
    print("(1) Adicionar Tarefa")
    print("(2) Exibir Lista de Tarefas")
    print("(3) Concluir Tarefa")
    print("(4) Excluir Tarefa")
    print("(0) Sair")
    imprimir_linha(lista)
    return ler_inteiro("Escolha uma opção: ")

def add_tarefa(lista: list) -> None:
    tarefa = input("Escreva a tarefa que deseja adicionar: ")
    dic = {"item": tarefa, "status": False}
    lista.append(dic)
    print(f"{VERDE}Tarefa adicionada com sucesso!{RESET}")

def exibe_lista(lista):
    if not lista:
        largura = obter_largura(lista)
        imprimir_linha(lista)
        print(f"{AMARELO}A lista está vazia!{RESET}".center(largura))
        imprimir_linha(lista)
        return

    maior_item = max(len(tarefa['item']) for tarefa in lista)
    largura = obter_largura(lista)

    imprimir_linha(lista)
    print(f"{AMARELO}Lista de Tarefas:{RESET}".center(largura + 10))
    imprimir_linha(lista)

    for i, tarefa in enumerate(lista):
        status_formatado = exibe_status(tarefa)
        print(f"{i + 1:>2} - {tarefa['item'] : <{maior_item}} | {status_formatado}")

    imprimir_linha(lista)

def concluir_tarefa(lista):
    if len(lista) == 0:
        largura = obter_largura(lista)
        imprimir_linha(lista)
        print(f"{AMARELO}A lista está vazia!{RESET}".center(largura))
        imprimir_linha(lista)
        return

    exibe_lista(lista)
    concluir = ler_inteiro("Digite o número da tarefa que deseja concluir: ")
    if 0 < concluir <= len(lista):
        lista[concluir - 1]["status"] = True
        print(f"{VERDE}Tarefa marcada como concluída!{RESET}")
    else:
        print(f"{VERMELHO}Número de tarefa inválido!{RESET}")

def excluir_tarefa(lista):
    if not lista:
        largura = obter_largura(lista)
        imprimir_linha(lista)
        print(f"{AMARELO}Não há tarefas para excluir.{RESET}".center(largura))
        imprimir_linha(lista)
        return

    exibe_lista(lista)
    excluir = ler_inteiro("Digite o número da tarefa que deseja excluir: ")
    if 0 < excluir <= len(lista):
        lista.pop(excluir - 1)
        print(f"{VERDE}Tarefa removida com sucesso!{RESET}")
    else:
        print(f"{VERMELHO}Número de tarefa inválido!{RESET}")

def exibe_status(tarefa):
    if not tarefa["status"]:
        return f"{VERMELHO}[❌ Pendente]{RESET}"
    else:
        return f"{VERDE}[✅ Concluída]{RESET}"

def pausa():
    input(f"{AMARELO}Pressione Enter para continuar...{RESET}")

def main():
    lista = carregar_dados()
    while True:
        limpar_tela()
        selecao = menu(lista)
        if selecao == 1:
            limpar_tela()
            add_tarefa(lista)
            salvar_dados(lista)
            pausa()
        elif selecao == 2:
            limpar_tela()
            exibe_lista(lista)
            pausa()
        elif selecao == 3:
            limpar_tela()
            concluir_tarefa(lista)
            salvar_dados(lista)
            pausa()
        elif selecao == 4:
            limpar_tela()
            excluir_tarefa(lista)
            salvar_dados(lista)
            pausa()
        elif selecao == 0:
            limpar_tela()
            mensagem_saida = "Obrigado por utilizar o Gerenciador! Até a próxima!"
            imprimir_linha(lista, texto=mensagem_saida)
            print(f"{VERDE}{mensagem_saida}{RESET}")
            imprimir_linha(lista, texto=mensagem_saida)
            salvar_dados(lista)
            break

if __name__ == "__main__":
    main()
