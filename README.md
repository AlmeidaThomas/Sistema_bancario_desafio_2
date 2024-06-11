# Sistema_bancario_desafio_2
# Criando um sistema simples bancario com uso de funções apenas e cadastro de usuarios e contas sem autenticação. Atividade da plataforma DIO.
import textwrap

def menu():
    menu = """\n
--------------------< MENU >--------------------
[1]\tDepositar
[2]\tSacar
[3]\tExtrato
[4]\tNova Conta
[5]\tListar Contas
[6]\tNovo Usuário
[0]\tSair
--> """
    return input(textwrap.dedent(menu))

def depositar(saldo, valor, extrato):
    if valor > 0:
        saldo += valor
        extrato.append(f"Depósito: +R${valor:.2f}")
    else:
        print("O valor do depósito deve ser positivo!")
    return saldo, extrato

def sacar(*, saldo, valor, extrato, limite, numero_saques, limite_saques):
    if valor > saldo:
        print("Saldo insuficiente!")
    elif valor > limite:
        print("O valor do saque excede o limite permitido!")
    elif numero_saques >= limite_saques:
        print("Número máximo de saques excedido!")
    else:
        saldo -= valor
        extrato.append(f"Saque: -R${valor:.2f}")
        numero_saques += 1
    return saldo, extrato, numero_saques

def exibir_extrato(saldo, *, extrato):
    print("\nExtrato:")
    if not extrato:
        print("Não foram realizadas movimentações.")
    else:
        for movimento in extrato:
            print(movimento)
    print(f"Saldo atual: R${saldo:.2f}")

def criar_usuario(usuarios, cpf, nome, data_nascimento, endereco):
    if any(usuario['cpf'] == cpf for usuario in usuarios):
        print("CPF já cadastrado!")
        return None
    novo_usuario = {
        'cpf': cpf,
        'nome': nome,
        'data_nascimento': data_nascimento,
        'endereco': endereco
    }
    usuarios.append(novo_usuario)
    print("Usuário cadastrado com sucesso!")
    return novo_usuario

def filtrar_usuario(cpf, usuarios):
    return next((usuario for usuario in usuarios if usuario['cpf'] == cpf), None)

def criar_conta(agencia, numero_conta, usuarios, cpf):
    usuario = filtrar_usuario(cpf, usuarios)
    if not usuario:
        print("Usuário não encontrado!")
        return None
    nova_conta = {
        'agencia': agencia,
        'numero_conta': numero_conta,
        'usuario': usuario
    }
    return nova_conta

def listar_contas(contas):
    for conta in contas:
        usuario = conta['usuario']
        print(f"Agência: {conta['agencia']} | Conta: {conta['numero_conta']} | Titular: {usuario['nome']}")

def main():
    usuarios = []
    contas = []
    extrato = []
    saldo = 0
    limite = 500
    numero_saques = 0
    limite_saques = 3
    numero_conta = 1
    agencia = '0001'

    while True:
        opcao = menu()

        if opcao == '1':
            valor = float(input("Informe o valor do depósito: "))
            saldo, extrato = depositar(saldo, valor, extrato)

        elif opcao == '2':
            valor = float(input("Informe o valor do saque: "))
            saldo, extrato, numero_saques = sacar(
                saldo=saldo, valor=valor, extrato=extrato, limite=limite, 
                numero_saques=numero_saques, limite_saques=limite_saques
            )

        elif opcao == '3':
            exibir_extrato(saldo, extrato=extrato)

        elif opcao == '4':
            cpf = input("Informe o CPF do usuário: ")
            nova_conta = criar_conta(agencia, numero_conta, usuarios, cpf)
            if nova_conta:
                contas.append(nova_conta)
                numero_conta += 1
                print(f"Conta {nova_conta['numero_conta']} criada com sucesso!")

        elif opcao == '5':
            listar_contas(contas)

        elif opcao == '6':
            cpf = input("Informe o CPF (somente números): ")
            nome = input("Informe o nome: ")
            data_nascimento = input("Informe a data de nascimento (DD/MM/AAAA): ")
            endereco = input("Informe o endereço (logradouro, nro - bairro - cidade/sigla estado): ")
            criar_usuario(usuarios, cpf, nome, data_nascimento, endereco)

        elif opcao == '0':
            break

        else:
            print("Opção inválida, tente novamente.")

if __name__ == "__main__":
