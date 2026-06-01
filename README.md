class ItemAutoPecas:
    def __init__(item, codigo, nome, quantidade, valor, estoque_minimo, estoque_maximo):
        item.codigo = codigo
        item.nome = nome
        item.quantidade = quantidade
        item.valor = valor
        item.estoque_minimo = estoque_minimo
        item.estoque_maximo = estoque_maximo

    def alterar_quantidade(item, nova_quantidade):
        if nova_quantidade < 0:
            return False, "A quantidade não pode ser negativa"
        elif nova_quantidade > item.estoque_maximo:
            return False, "Quantidade superior ao estoque máximo"
        else:
            item.quantidade = nova_quantidade
            return True, None

    def alterar_valor(item, novo_valor):
        if novo_valor < 0:
            return False, "O valor não pode ser negativo"
        else:
            item.valor = novo_valor
            return True, None

    def esta_em_falta(item):
        return item.quantidade < item.estoque_minimo

    def __str__(item):
        return (
            f"Código: {item.codigo}, Nome: {item.nome}, Quantidade: {item.quantidade}, "
            f"Valor: R$ {item.valor:.2f}, Estoque mínimo: {item.estoque_minimo}, "
            f"Estoque máximo: {item.estoque_maximo}"
        )


def parse_int(valor):
    texto = str(valor).strip()
    if texto.startswith("-"):
        texto_n = texto[1:]
    else:
        texto_n = texto
    if texto_n.isdigit():
        return int(texto), None
    else:
        return None, "Valor inteiro inválido"


def parse_float(valor):
    texto = str(valor).strip()
    if texto.count(".") == 1:
        inteiro, decimal = texto.split(".", 1)
        if inteiro.startswith("-"):
            inteiro_base = inteiro[1:]
        else:
            inteiro_base = inteiro
        if (inteiro_base.isdigit() or inteiro_base == "") and decimal.isdigit():
            return float(texto), None
        else:
            return None, "Valor numérico inválido"
    elif texto.startswith("-") and texto[1:].isdigit():
        return float(texto), None
    elif texto.isdigit():
        return float(texto), None
    else:
        return None, "Valor numérico inválido"


def cadastrar_item(itens, codigo, nome, quantidade, valor, estoque_minimo, estoque_maximo):
    if codigo in itens:
        return None, "Já existe um item com esse código"
    else:
        item = ItemAutoPecas(codigo, nome, quantidade, valor, estoque_minimo, estoque_maximo)
        itens[codigo] = item
        return item, None


def listar_itens(itens):
    if not itens:
        print("Nenhum item cadastrado.")
        return
    for item in itens.values():
        print(item)
        if item.esta_em_falta():
            print("  Atenção: estoque abaixo do mínimo.")


def buscar_item(itens, codigo):
    return itens.get(codigo)


def main():
    itens = {}

    while True:
        print("\n=== Cadastro de Auto Peças ===")
        print("1. Cadastrar item")
        print("2. Listar itens")
        print("3. Alterar quantidade")
        print("4. Alterar valor")
        print("5. Sair")
        opcao = input("Escolha uma opção: ")

        if opcao == "1":
            codigo = input("Código do item: ")
            nome = input("Nome do item: ")
            quantidade_texto = input("Quantidade em estoque: ")
            valor_texto = input("Valor unitário: ")
            estoque_minimo_texto = input("Estoque mínimo: ")
            estoque_maximo_texto = input("Estoque máximo: ")

            quantidade, erro_qtd = parse_int(quantidade_texto)
            valor, erro_valor = parse_float(valor_texto)
            estoque_minimo, erro_min = parse_int(estoque_minimo_texto)
            estoque_maximo, erro_max = parse_int(estoque_maximo_texto)

            if erro_qtd:
                print("Erro ao cadastrar item:", erro_qtd)
            elif erro_valor:
                print("Erro ao cadastrar item:", erro_valor)
            elif erro_min:
                print("Erro ao cadastrar item:", erro_min)
            elif erro_max:
                print("Erro ao cadastrar item:", erro_max)
            else:
                item, erro = cadastrar_item(itens, codigo, nome, quantidade, valor, estoque_minimo, estoque_maximo)
                if erro:
                    print("Erro ao cadastrar item:", erro)
                else:
                    print("Item cadastrado:", item)

        elif opcao == "2":
            listar_itens(itens)

        elif opcao == "3":
            codigo = input("Código do item: ")
            item = buscar_item(itens, codigo)
            if item:
                nova_qtd_texto = input("Nova quantidade em estoque: ")
                nova_qtd, erro_qtd = parse_int(nova_qtd_texto)
                if erro_qtd:
                    print("Erro ao alterar quantidade:", erro_qtd)
                else:
                    ok, erro = item.alterar_quantidade(nova_qtd)
                    if ok:
                        print("Quantidade atualizada:", item)
                    else:
                        print("Erro ao alterar quantidade:", erro)
            else:
                print("Item não encontrado.")

        elif opcao == "4":
            codigo = input("Código do item: ")
            item = buscar_item(itens, codigo)
            if item:
                novo_valor_texto = input("Novo valor unitário: ")
                novo_valor, erro_valor = parse_float(novo_valor_texto)
                if erro_valor:
                    print("Erro ao alterar valor:", erro_valor)
                else:
                    ok, erro = item.alterar_valor(novo_valor)
                    if ok:
                        print("Valor atualizado:", item)
                    else:
                        print("Erro ao alterar valor:", erro)
            else:
                print("Item não encontrado.")

        elif opcao == "5":
            print("Saindo...")
            break

        else:
            print("Opção inválida. Tente novamente.")


if __name__ == "__main__":
    main()
