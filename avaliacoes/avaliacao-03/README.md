#avaliacao3


import 'dart:async';

Future<void> main() async {
  print('=== Iniciando Sistema Escolar Assíncrono ===\n');

  try {
    // 1. Cria/Conecta ao banco de dados de forma assíncrona
    final db = await DatabaseConnection.open('alunos.db');

    // 2. Cria a tabela se não existir
    await db.execute('''
      CREATE TABLE IF NOT EXISTS tb_alunos (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        nome TEXT NOT NULL,
        idade INTEGER NOT NULL
      );
    ''');

    // 3. Inclui três alunos na tabela de forma assíncrona
    final alunosParaInserir = [
      {'nome': 'Ana Silva', 'idade': 20},
      {'nome': 'Carlos Oliveira', 'idade': 22},
      {'nome': 'Beatriz Souza', 'idade': 19},
    ];

    print('Inserindo alunos...');
    for (var aluno in alunosParaInserir) {
      await db.insert('tb_alunos', aluno);
    }
    print('✅ 3 alunos inseridos com sucesso.\n');

    // 4. Lista o conteúdo da tabela usando Streams (Assíncrono)
    print('--- Lista de Alunos (Consulta Assíncrona) ---');
    final Stream<Map<String, dynamic>> streamAlunos = db.select('SELECT * FROM tb_alunos');
    
    await for (final aluno in streamAlunos) {
      print('ID: ${aluno['id']} | Nome: ${aluno['nome']} | Idade: ${aluno['idade']}');
    }
    print('---------------------------------------------\n');

  } on DatabaseException catch (e) {
    print('🚨 Erro de Banco de Dados: ${e.message}');
  } on TimeoutException catch (e) {
    print('⏳ Erro de Tempo Limite: O banco demorou muito para responder. $e');
  } catch (e) {
    print('❌ Erro Inesperado: $e');
  } finally {
    print('=== Execução Concluída ===');
  }
}

// =================================================================
// SIMULAÇÃO ASSÍNCRONA DE BANCO DE DADOS (Compatível com DartPad)
// =================================================================

class DatabaseConnection {
  final String path;
  final List<Map<String, dynamic>> _storage = [];
  int _currentId = 1;
  bool _isTableCreated = false;

  DatabaseConnection._(this.path);

  // Simula a abertura assíncrona do arquivo .db
  static Future<DatabaseConnection> open(String dbPath) async {
    try {
      await Future.delayed(Duration(milliseconds: 600)); // Simula I/O de disco
      if (dbPath.isEmpty) throw DatabaseException('Caminho do banco inválido.');
      print('📦 Banco de dados "$dbPath" verificado/criado com sucesso.');
      return DatabaseConnection._(dbPath);
    } catch (e) {
      throw DatabaseException('Falha ao abrir o banco de dados: $e');
    }
  }

  // Simula execução de comandos (DDL)
  Future<void> execute(String sql) async {
    try {
      await Future.delayed(Duration(milliseconds: 400));
      if (!sql.toUpperCase().contains('CREATE TABLE')) {
        throw DatabaseException('Sintaxe SQL incorreta para execução.');
      }
      _isTableCreated = true;
      print('📐 Tabela "tb_alunos" verificada/criada com sucesso.');
    } catch (e) {
      throw DatabaseException('Falha ao executar comando estrutural: $e');
    }
  }

  // Simula a inserção assíncrona de dados
  Future<void> insert(String table, Map<String, dynamic> data) async {
    try {
      await Future.delayed(Duration(milliseconds: 300));
      if (!_isTableCreated) {
        throw DatabaseException('Tabela "$table" não existe.');
      }
      if (data['nome'] == null || data['idade'] == null) {
        throw DatabaseException('Campos obrigatórios ausentes.');
      }
      
      _storage.add({
        'id': _currentId++,
        'nome': data['nome'],
        'idade': data['idade'],
      });
    } catch (e) {
      throw DatabaseException('Erro ao inserir registro: $e');
    }
  }

  // Simula a consulta assíncrona retornando os dados linha por linha (Stream)
  Stream<Map<String, dynamic>> select(String sql) async* {
    try {
      await Future.delayed(Duration(milliseconds: 500));
      if (!_isTableCreated) {
        throw DatabaseException('Tabela não inicializada para consulta.');
      }

      for (var row in _storage) {
        await Future.delayed(Duration(milliseconds: 150)); // Simula leitura linha por linha
        yield row;
      }
    } catch (e) {
      throw DatabaseException('Erro ao ler dados da tabela: $e');
    }
  }
}

// Exceção customizada para isolar erros do banco
class DatabaseException implements Exception {
  final String message;
  DatabaseException(this.message);
  @override
  String toString() => 'DatabaseException: $message';
}

