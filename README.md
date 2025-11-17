import sqlite3
from random import choice, randint

# ============================
# CONECTAR AO BANCO
# ============================
conn = sqlite3.connect("escola.db")
cur = conn.cursor()

# ============================
# CRIAR TABELA SE NÃO EXISTIR
# ============================
cur.execute("""
CREATE TABLE IF NOT EXISTS alunos (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    nome TEXT,
    idade INTEGER,
    curso TEXT
)
""")
conn.commit()

# ============================
# FUNÇÕES DO SISTEMA
# ============================

def inserir_aluno():
    nome = input("Nome do aluno: ")
    idade = int(input("Idade: "))
    curso = input("Curso: ")

    cur.execute("INSERT INTO alunos (nome, idade, curso) VALUES (?, ?, ?)",
                (nome, idade, curso))
    conn.commit()
    print("\nAluno cadastrado com sucesso!\n")


def listar_alunos():
    cur.execute("SELECT * FROM alunos")
    alunos = cur.fetchall()

    if not alunos:
        print("\nNenhum aluno cadastrado.\n")
        return

    print("\n--- LISTA DE ALUNOS ---")
    for a in alunos:
        print(f"ID: {a[0]}  |  Nome: {a[1]}  |  Idade: {a[2]}  |  Curso: {a[3]}")
    print()


def atualizar_idade():
    listar_alunos()
    aluno_id = int(input("ID do aluno para atualizar: "))
    nova_idade = int(input("Nova idade: "))

    cur.execute("UPDATE alunos SET idade = ? WHERE id = ?", (nova_idade, aluno_id))
    conn.commit()

    if cur.rowcount > 0:
        print("\nIdade atualizada com sucesso!\n")
    else:
        print("\nID não encontrado!\n")


def excluir_aluno():
    listar_alunos()
    aluno_id = int(input("ID do aluno para excluir: "))

    cur.execute("DELETE FROM alunos WHERE id = ?", (aluno_id,))
    conn.commit()

    if cur.rowcount > 0:
        print("\nAluno excluído com sucesso!\n")
    else:
        print("\nID não encontrado!\n")


def adicionar_40_alunos():
    nomes = [
        "Ana", "Bruno", "Carlos", "Daniela", "Eduardo", "Fernanda", "Gustavo", "Helena",
        "Igor", "Joana", "Kleber", "Larissa", "Marcos", "Natália", "Otávio", "Patrícia",
        "Quésia", "Rafael", "Sabrina", "Tiago", "Ursula", "Vinícius", "William", "Xênia",
        "Yasmin", "Zeca", "Bianca", "Diego", "Elaine", "Felipe", "Georgia", "Hugo",
        "Isabela", "Juliano", "Karen", "Leandro", "Mirela", "Nicolas"
    ]

    cursos = ["Python", "Informática", "Robótica", "Design", "TI", "Administração"]

    for _ in range(40):
        nome = choice(nomes)
        idade = randint(14, 25)
        curso = choice(cursos)

        cur.execute("INSERT INTO alunos (nome, idade, curso) VALUES (?, ?, ?)",
                    (nome, idade, curso))

    conn.commit()
    print("\n✔ 40 alunos adicionados com sucesso!\n")


# ============================
# MENU PRINCIPAL
# ============================

while True:
    print("""
=== SISTEMA DE CADASTRO DE ALUNOS ===

1 - Inserir aluno
2 - Listar alunos
3 - Atualizar idade
4 - Excluir aluno
5 - Sair
6 - Adicionar 40 alunos automáticos
""")

    opcao = input("Escolha uma opção: ")

    if opcao == "1":
        inserir_aluno()
    elif opcao == "2":
        listar_alunos()
    elif opcao == "3":
        atualizar_idade()
    elif opcao == "4":
        excluir_aluno()
    elif opcao == "5":
        print("\nSaindo do sistema...\n")
        break
    elif opcao == "6":
        adicionar_40_alunos()
    else:
        print("\nOpção inválida! Tente novamente.\n")

conn.close()
