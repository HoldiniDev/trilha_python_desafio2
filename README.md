# trilha_python_desafio2
Melhorias feitas no código do mini sistema bancário, agora utilizando-se de funções em python, exercicio do bootcamp santander.


"""
DESAFIO #2 - CRIAR UM SISTEMA BANCÁRIO´v2

PREMISSAS:
1. Criar um sistema bancário que realize as seguintes operações: Saque, Depósito e Extrato - OK;
2. Criar uma função de cadastro de usuário que colete (nome, data de nascimento, cpf, e endereço (logradouro - nro - bairro - cidade/estado))
3. Validar se o CPF ja existe no cadastro não permitindo cadastrar o mesmo CPF mais de uma vez.
4. O programa deve armazenar as contas numa lista (agencia, número da conta e usuário) - O número da conta é sequencial começando em 1 - Agencia fixo "0001";
5. Um usuário pode ter mais de uma conta;
6. Criar funções para as ações existentes (Depósito, Saque, Extrato).
7. Função Saque deve receber argumentos apenas por nome (keyword only) - saldo, valor, extrato, limite, numero_saques, limite_saques
8. Função Depósito deve receber argumentos posicionais - saldo, valor, extrato.

Data da versão 2: 02/07/2025
Autor: Gabriel S.

"""

import textwrap

######################################################
##             BIBLIOTECA DE FUNÇÕES                ##
######################################################


def menu():
    ######################################################
    ##                     MENU                         ##
    ######################################################

    menu = """
    ################ SISTEMA BANCÁRIO 2.0 ################
        1. [c] Cadastro de Cliente
        2. [n] Cadastro Nova Conta
        3. [d] Depósito
        4. [e] Extrato
        5. [s] Saque
        6. [f] Finaliza
    ######################################################    
    """
    return input(textwrap.dedent(menu))

def cadastro_cliente(usuarios):
    cpf = str(input('Digite seu CPF (apenas números: )'))
    usuario = filtrar_usuario(cpf, usuarios)

    if usuario:
        print('Já existe um usuário com este CPF')
        return

    nome = str(input('Insira seu nome completo: '))
    data_nasc = str(input('Insira sua data de nascimento no formato [DD/MM/AAAA]: '))
    logradouro = str(input('Digite o nome da Rua/Avenida do seu endereço: '))
    numero = str(input('Digite o numero: '))
    bairro = str(input('Digite o bairro em que reside: '))
    cidade = str(input('Digite o nome da cidade do seu endereço: '))

    usuarios.append({'nome':nome, 'data_nasc':data_nasc, 'cpf':cpf, 'endereco':logradouro + '-' + numero + '-' + bairro + '-' + cidade})
    print('Usuário Cadastrado com sucesso!!!')

#    print('Tamanho da lista de Clientes: ' + str(len(clientes)))
#    print(clientes)

def filtrar_usuario(cpf, usuarios):
    usuarios_filtrados = [usuario for usuario in usuarios if usuario['cpf'] == cpf]
    return usuarios_filtrados[0] if usuarios_filtrados else None


def cria_conta(agencia, numero_conta, usuarios):
    cpf = input('Informe o CPF do usuário: ')
    usuario = filtrar_usuario(cpf, usuarios)

    if usuario:
        print('Conta criada com sucessso!')
        return {'agencia': agencia, 'numero_conta': numero_conta, 'usuario': usuario}

    print('Usuário não encontrado, fluxo de criação de conta encerrado.')


def insere_deposito(saldo, valor, extrato, ):
    if valor <= 0:
        print('Você inseriu um valor não permitido para depósito, tente novamente...')
    else:
        saldo += valor
        print(f'Seu depósito foi realizado com sucesso!!!')
        extrato += 'Depósito .................... R$ ' + str(valor) + '\n'

    return saldo, extrato

def retira_saque(*, saldo, valor, extrato, limite, numero_saques, limite_saques):
    excedeu_saldo = valor > saldo
    excedeu_limite = valor > limite
    excedeu_saques = numero_saques > limite_saques

    if excedeu_saldo:
        print('Saldo insuficiente, tente um valor menor...')

    elif excedeu_limite:
        print('Você excedeu o limite de valor, tente um valor menor...')

    elif excedeu_saques:
        print('Você excedeu o número máximo de saques no dia.')

    elif valor > 0:
        saldo -= valor
        extrato += 'Saque    .................... R$ ' + str(valor) + '\n'
        numero_saques += 1
        print('Saque realizado com sucesso!!')
        print(limite_saques)
        print(numero_saques)
        print(saldo)

    return saldo, extrato, numero_saques

def gera_extrato(saldo, *, extrato):
    print(extrato)
    print('\n Saldo .................R$ ' + str(saldo))


######################################################
##                EXTRATO DA CONTA                  ##
######################################################

def main():

    ## Inicializa as variaveis
    LIMITE_SAQUES=3
    AGENCIA='0001'

    saldo = 0
    limite = 500
    extrato = ''
    numero_saques = 0
    usuarios = []
    contas = []

    ## Insere a opção desejada

    while True:
        opcao = menu()

    ## Classifica a opcao

        ## BLOCO DEPÓSITO
        if opcao.upper() == 'D':
            valor = int(input('Digite o valor a ser depositado'))
            saldo, extrato = insere_deposito(saldo, valor, extrato)

        ## BLOCO SAQUE
        elif opcao.upper() == 'S':
            valor = int(input('Digite o valor a ser sacado'))
            saldo, extrato, numero_saques = retira_saque(
                saldo=saldo,
                valor=valor,
                extrato=extrato,
                limite=limite,
                numero_saques=numero_saques,
                limite_saques=LIMITE_SAQUES
            )

        ## BLOCO EXTRATO
        elif opcao.upper() == 'E':
            gera_extrato(saldo, extrato=extrato)

        elif opcao.upper() == 'C':
            cadastro_cliente(usuarios)

        elif opcao.upper() == 'N':
            numero_conta = len(contas) + 1
            conta = cria_conta(AGENCIA, numero_conta, usuarios)

            if conta:
                contas.append(conta)

        elif opcao.upper() == 'LC':
            listar_contas(contas)

        ## BLOCO FINALIZA SESSÃO
        elif opcao.upper() == 'F':
            print('Fim da Execução, volte sempre')
            break

        ## BLOCO OPÇÃO INVÁLIDA
        elif opcao.upper not in ('D', 'S', 'E', 'F', 'C', 'NC', 'LC'):
            print('Opção Invalida')


main()
