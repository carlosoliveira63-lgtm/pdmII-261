  #avaliacoes02

  import 'dart:convert';

class Dependente {
  final String _nome;

  Dependente(this._nome);

  Map<String, dynamic> toJson() => {
    'nome': _nome,
  };
}

class Funcionario {
  final String _nome;
  final List<Dependente> _dependentes;

  Funcionario(this._nome, this._dependentes);

  Map<String, dynamic> toJson() => {
    'nome': _nome,
    'dependentes': _dependentes.map((d) => d.toJson()).toList(),
  };
}

class EquipeProjeto {
  final String _nomeProjeto;
  final List<Funcionario> _funcionarios;

  EquipeProjeto(this._nomeProjeto, this._funcionarios);

  Map<String, dynamic> toJson() => {
    'nomeProjeto': _nomeProjeto,
    'funcionarios': _funcionarios.map((f) => f.toJson()).toList(),
  };
}

void main() {
  var dep1 = Dependente("Pedro Silva");
  var dep2 = Dependente("Maria Silva");
  var dep3 = Dependente("Lucas Oliveira");

  var func1 = Funcionario("João Silva", [dep1, dep2]);
  var func2 = Funcionario("Ana Oliveira", [dep3]);
  var func3 = Funcionario("Carlos Souza", []);

  List<Funcionario> listaFuncionarios = [func1, func2, func3];

  var equipeTI = EquipeProjeto("Lista de Funcionarios", listaFuncionarios);

  String resultadoJson = JsonEncoder.withIndent('  ').convert(equipeTI);
  print(resultadoJson);
}

