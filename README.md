# SLA
banco de dados bosta
import sqlite3
from flask import Flask, request, jsonify

app = Flask(__name__)
DATABASE = 'banco.db'

def criar_bd():
    conn = sqlite3.connect(DATABASE)
    cursor = conn.cursor()
    cursor.execute('''
        CREATE TABLE IF NOT EXISTS registros (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            nome TEXT NOT NULL,
            email TEXT NOT NULL
        )
    ''')
    conn.commit()
    conn.close()

criar_bd()

@app.route('/registros', methods=['GET'])
def obter_registros():
    conn = sqlite3.connect(DATABASE)
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM registros")
    registros = cursor.fetchall()
    conn.close()
    return jsonify(registros)

@app.route('/registros', methods=['POST'])
def adicionar_registro():
    dados = request.get_json()
    nome = dados['nome']
    email = dados['email']
    
    conn = sqlite3.connect(DATABASE)
    cursor = conn.cursor()
    cursor.execute("INSERT INTO registros (nome, email) VALUES (?, ?)", (nome, email))
    conn.commit()
    conn.close()
    return jsonify({'mensagem': 'Registro adicionado com sucesso!'}), 201

if __name__ == '__main__':
    app.run(debug=True)
