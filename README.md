# sinduscon
app sindicato , tela de cadastro e busca usando python mysql e streamlit
import streamlit as st
import mysql.connector
import pandas as pd
from PIL import Image

# Função para autenticar o usuário no banco de dados
def autenticar_usuario(email, senha):
    conn = mysql.connector.connect(
        host="localhost",
        database="sindicato",
        user="root",
        password="root"
    )
    cursor = conn.cursor()
    query = "SELECT * FROM cadastros_usuario WHERE email = %s AND senha = %s"
    values = (email, senha)
    cursor.execute(query, values)
    result = cursor.fetchone()
    cursor.close()
    conn.close()
    return result

# Função para exibir os dados do usuário
def exibir_dados_usuario(result):
    st.subheader("Dados do Usuário")
    id_usuario, nome, email, cpf_cnpj, endereco, bairro, telefone, prestador_cliente, senha, sobrenome, idade, nome_empresa, id_profissao, id_cidade = result
    dados_usuario = {
        "ID Usuário": [id_usuario],
        "Nome": [nome],
        "Email": [email],
        "CPF/CNPJ": [cpf_cnpj],
        "Endereço": [endereco],
        "Bairro": [bairro],
        "Telefone": [telefone],
        "Prestador/Cliente": [prestador_cliente],
        "Senha": [senha],
        "Sobrenome": [sobrenome],
        "Idade": [idade],
        "Nome da Empresa": [nome_empresa],
        "ID Profissão": [id_profissao],
        "ID Cidade": [id_cidade]
    }
    df = pd.DataFrame.from_dict(dados_usuario)
    st.dataframe(df)

# Função para cadastrar o usuário no banco de dados
def cadastrar_usuario(nome, email, cpf_cnpj, endereco, bairro, telefone, prestador_cliente, senha, sobrenome, idade, nome_empresa, id_profissao, id_cidade):
    conn = mysql.connector.connect(
        host="localhost",
        database="sindicato",
        user="root",
        password="root"
    )
    cursor = conn.cursor()
    query = "INSERT INTO cadastros_usuario (nome, email, cpf_cnpj, endereco, bairro, telefone, prestador_cliente, senha, sobrenome, idade, nome_empresa, id_profissao, id_cidade) VALUES (%s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s)"
    values = (nome, email, cpf_cnpj, endereco, bairro, telefone, prestador_cliente, senha, sobrenome, idade, nome_empresa, id_profissao, id_cidade)
    cursor.execute(query, values)
    conn.commit()
    cursor.close()
    conn.close()

# Função para buscar usuário por nome
def buscar_usuario_por_nome(nome):
    conn = mysql.connector.connect(
        host="localhost",
        database="sindicato",
        user="root",
        password="root"
    )
    cursor = conn.cursor(dictionary=True)
    query = "SELECT * FROM cadastros_usuario WHERE nome = %s"
    values = (nome,)
    cursor.execute(query, values)
    result = cursor.fetchone()
    cursor.close()
    conn.close()
    return result

# Função para exibir a tela de autenticação
def exibir_tela_autenticacao():
    # Exibir os campos de entrada
    st.subheader("Autenticação")
    email = st.text_input("Email")
    senha = st.text_input("Senha", type="password")

    # Verificar se o usuário foi autenticado
    if st.button("Entrar"):
        result = autenticar_usuario(email, senha)
        if result:
            exibir_dados_usuario(result)
        else:
            st.error("Email ou senha incorretos")

# Função para exibir a tela de cadastro
def exibir_tela_cadastro():
    # Exibir os campos de entrada
    st.subheader("Cadastro")
    nome = st.text_input("Nome")
    email = st.text_input("Email")
    cpf_cnpj = st.text_input("CPF/CNPJ")
    endereco = st.text_input("Endereço")
    bairro = st.text_input("Bairro")
    telefone = st.text_input("Telefone")
    prestador_cliente = st.selectbox("Prestador/Cliente", ["Prestador", "Cliente"])
    senha = st.text_input("Senha", type="password")
    sobrenome = st.text_input("Sobrenome")
    idade = st.number_input("Idade")
    nome_empresa = st.text_input("Nome da Empresa")
    id_profissao = st.number_input("ID Profissão")
    id_cidade = st.number_input("ID Cidade")

    # Verificar se o usuário clicou em "Cadastrar"
    if st.button("Cadastrar"):
        cadastrar_usuario(nome, email, cpf_cnpj, endereco, bairro, telefone, prestador_cliente, senha, sobrenome, idade, nome_empresa, id_profissao, id_cidade)
        st.success("Usuário cadastrado com sucesso")

# Função para exibir a tela de busca por nome
def exibir_tela_busca_nome():
    # Exibir o campo de busca
    st.subheader("Buscar Usuário por Nome")
    nome = st.text_input("Nome")

    # Verificar se o usuário clicou em "Buscar"
    if st.button("Buscar"):
        result = buscar_usuario_por_nome(nome)
        if result:
            exibir_dados_usuario(result)
        else:
            st.error("Usuário não encontrado")

# Configuração da página Streamlit
def main():
    # Configuração da página
    st.set_page_config(page_title="Sindicato", page_icon=":thumbsup:", layout="wide")

    # Título
    st.title("Sistema do Sindicato")

    # Carregar a imagem de fundo
    logo_image = Image.open("logo_sind.jpg")

    # Exibir a imagem de fundo
    st.image(logo_image, use_column_width=True)

    # Seleção de tela
    tela_selecionada = st.sidebar.radio("Menu", ["Autenticação", "Cadastro", "Buscar por Nome"])

    # Exibir tela selecionada
    if tela_selecionada == "Autenticação":
        exibir_tela_autenticacao()
    elif tela_selecionada == "Cadastro":
        exibir_tela_cadastro()
    elif tela_selecionada == "Buscar por Nome":
        exibir_tela_busca_nome()

# Executar o programa
if __name__ == "__main__":
    main()
