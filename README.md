import React, { useState, useEffect } from 'react';
import { PlusCircle, MinusCircle, Trash2, DollarSign, TrendingUp, TrendingDown } from 'lucide-react';

const OrcamentoCalculadora = () => {
  const [transacoes, setTransacoes] = useState([]);
  const [novaTransacao, setNovaTransacao] = useState({
    descricao: '',
    valor: '',
    categoria: '',
    tipo: 'despesa'
  });

  const categoriasDespesas = [
    'Alimentação', 'Transporte', 'Moradia', 'Saúde', 'Educação', 
    'Entretenimento', 'Roupas', 'Outros'
  ];

  const categoriasReceitas = [
    'Salário', 'Freelance', 'Investimentos', 'Vendas', 'Outros'
  ];

  const adicionarTransacao = () => {
    if (!novaTransacao.descricao || !novaTransacao.valor || !novaTransacao.categoria) {
      alert('Por favor, preencha todos os campos');
      return;
    }

    const transacao = {
      id: Date.now(),
      descricao: novaTransacao.descricao,
      valor: parseFloat(novaTransacao.valor),
      categoria: novaTransacao.categoria,
      tipo: novaTransacao.tipo,
      data: new Date().toLocaleDateString('pt-BR')
    };

    setTransacoes([...transacoes, transacao]);
    setNovaTransacao({
      descricao: '',
      valor: '',
      categoria: '',
      tipo: 'despesa'
    });
  };

  const removerTransacao = (id) => {
    setTransacoes(transacoes.filter(t => t.id !== id));
  };

  const calcularTotais = () => {
    const receitas = transacoes
      .filter(t => t.tipo === 'receita')
      .reduce((total, t) => total + t.valor, 0);
    
    const despesas = transacoes
      .filter(t => t.tipo === 'despesa')
      .reduce((total, t) => total + t.valor, 0);

    const saldo = receitas - despesas;

    return { receitas, despesas, saldo };
  };

  const formatarMoeda = (valor) => {
    return new Intl.NumberFormat('pt-BR', {
      style: 'currency',
      currency: 'BRL'
    }).format(valor);
  };

  const { receitas, despesas, saldo } = calcularTotais();

  return (
    <div className="max-w-4xl mx-auto p-6 bg-gray-50 min-h-screen">
      <div className="bg-white rounded-lg shadow-lg p-6 mb-6">
        <h1 className="text-3xl font-bold text-gray-800 mb-6 text-center flex items-center justify-center gap-2">
          <DollarSign className="text-green-600" />
          Calculadora de Orçamento Pessoal
        </h1>

        {/* Cards de Resumo */}
        <div className="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
          <div className="bg-green-100 p-4 rounded-lg border-l-4 border-green-500">
            <div className="flex items-center justify-between">
              <div>
                <p className="text-green-700 text-sm font-medium">Receitas</p>
                <p className="text-2xl font-bold text-green-800">{formatarMoeda(receitas)}</p>
              </div>
              <TrendingUp className="text-green-600" size={32} />
            </div>
          </div>

          <div className="bg-red-100 p-4 rounded-lg border-l-4 border-red-500">
            <div className="flex items-center justify-between">
              <div>
                <p className="text-red-700 text-sm font-medium">Despesas</p>
                <p className="text-2xl font-bold text-red-800">{formatarMoeda(despesas)}</p>
              </div>
              <TrendingDown className="text-red-600" size={32} />
            </div>
          </div>

          <div className={`p-4 rounded-lg border-l-4 ${saldo >= 0 ? 'bg-blue-100 border-blue-500' : 'bg-red-100 border-red-500'}`}>
            <div className="flex items-center justify-between">
              <div>
                <p className={`text-sm font-medium ${saldo >= 0 ? 'text-blue-700' : 'text-red-700'}`}>Saldo</p>
                <p className={`text-2xl font-bold ${saldo >= 0 ? 'text-blue-800' : 'text-red-800'}`}>
                  {formatarMoeda(saldo)}
                </p>
              </div>
              <DollarSign className={saldo >= 0 ? 'text-blue-600' : 'text-red-600'} size={32} />
            </div>
          </div>
        </div>

        {/* Formulário */}
        <div className="bg-gray-50 p-4 rounded-lg mb-6">
          <h2 className="text-xl font-semibold mb-4 text-gray-800">Nova Transação</h2>
          
          <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-5 gap-4">
            <input
              type="text"
              placeholder="Descrição"
              value={novaTransacao.descricao}
              onChange={(e) => setNovaTransacao({...novaTransacao, descricao: e.target.value})}
              className="px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
            />

            <input
              type="number"
              step="0.01"
              placeholder="Valor"
              value={novaTransacao.valor}
              onChange={(e) => setNovaTransacao({...novaTransacao, valor: e.target.value})}
              className="px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
            />

            <select
              value={novaTransacao.tipo}
              onChange={(e) => setNovaTransacao({...novaTransacao, tipo: e.target.value, categoria: ''})}
              className="px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
            >
              <option value="despesa">Despesa</option>
              <option value="receita">Receita</option>
            </select>

            <select
              value={novaTransacao.categoria}
              onChange={(e) => setNovaTransacao({...novaTransacao, categoria: e.target.value})}
              className="px-3 py-2 border border-gray-300 rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
            >
              <option value="">Selecione a categoria</option>
              {(novaTransacao.tipo === 'despesa' ? categoriasDespesas : categoriasReceitas).map(cat => (
                <option key={cat} value={cat}>{cat}</option>
              ))}
            </select>

            <button
              onClick={adicionarTransacao}
              className="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-md flex items-center justify-center gap-2 transition-colors"
            >
              <PlusCircle size={20} />
              Adicionar
            </button>
          </div>
        </div>

        {/* Lista de Transações */}
        <div className="bg-white">
          <h2 className="text-xl font-semibold mb-4 text-gray-800">Histórico de Transações</h2>
          
          {transacoes.length === 0 ? (
            <p className="text-gray-500 text-center py-8">Nenhuma transação adicionada ainda.</p>
          ) : (
            <div className="space-y-2 max-h-96 overflow-y-auto">
              {transacoes.slice().reverse().map(transacao => (
                <div
                  key={transacao.id}
                  className={`flex items-center justify-between p-3 rounded-lg border-l-4 ${
                    transacao.tipo === 'receita' 
                      ? 'bg-green-50 border-green-500' 
                      : 'bg-red-50 border-red-500'
                  }`}
                >
                  <div className="flex-1">
                    <div className="flex items-center gap-2">
                      {transacao.tipo === 'receita' ? 
                        <PlusCircle className="text-green-600" size={16} /> :
                        <MinusCircle className="text-red-600" size={16} />
                      }
                      <span className="font-medium text-gray-800">{transacao.descricao}</span>
                    </div>
                    <div className="text-sm text-gray-600 mt-1">
                      {transacao.categoria} • {transacao.data}
                    </div>
                  </div>
                  
                  <div className="flex items-center gap-3">
                    <span className={`font-bold text-lg ${
                      transacao.tipo === 'receita' ? 'text-green-600' : 'text-red-600'
                    }`}>
                      {transacao.tipo === 'receita' ? '+' : '-'}{formatarMoeda(transacao.valor)}
                    </span>
                    
                    <button
                      onClick={() => removerTransacao(transacao.id)}
                      className="text-red-500 hover:text-red-700 p-1 transition-colors"
                    >
                      <Trash2 size={16} />
                    </button>
                  </div>
                </div>
              ))}
            </div>
          )}
        </div>
      </div>
    </div>
  );
};

export default OrcamentoCalculadora;
