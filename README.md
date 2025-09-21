<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Impressão Rápida</title>
<style>
  body { font-family: Arial, sans-serif; background-color: #e0f7fa; margin: 0; padding: 0; }
  .container { max-width: 720px; margin: 40px auto; background: #ffffff; padding: 40px; border-radius: 16px; box-shadow: none; }
  h1 { text-align: center; font-size: 32px; margin-bottom: 25px; }
  label { display: block; margin: 15px 0 5px; font-weight: 600; }
  input[type=file], input[type=text], select, input[type=date] { width: 100%; padding: 12px; border-radius: 8px; border: 1px solid #bbb; box-sizing: border-box; font-size: 16px; }
  .checkbox { display: flex; align-items: center; margin-top: 12px; }
  .checkbox input { margin-right: 10px; }
  .button { display: block; width: 100%; padding: 16px; margin-top: 25px; background: #00acc1; color: #fff; border: none; border-radius: 8px; cursor: pointer; font-size: 18px; font-weight: bold; transition: 0.3s; }
  .button:hover { background: #00838f; }
  .strike { text-decoration: line-through; color: #d32f2f; font-weight: bold; font-size: 16px; margin-top: 10px; }
  .note { font-size: 13px; color: #555; margin-top: 6px; }
  table { width: 100%; border-collapse: collapse; margin-top: 15px; }
  table, th, td { border: 1px solid #bbb; }
  th, td { padding: 10px; text-align: center; }
  #localizacao { display: none; margin-top: 15px; }
  .ocupado { background-color: #f8d7da; color: #721c24; }
</style>
</head>
<body>
<div class="container" id="container">
  <div id="etapa1">
    <h1>Impressão Rápida</h1>

    <table>
      <thead><tr><th>Serviço</th><th>Preço</th></tr></thead>
      <tbody>
        <tr><td>Impressão Comum</td><td>R$ 3,50</td></tr>
      </tbody>
    </table>

    <label class="strike">Xerox (Em manutenção)</label>
    <label for="arquivo">Enviar arquivo:</label>
    <input type="file" id="arquivo">

    <div class="checkbox">
      <input type="checkbox" id="enviarPdf">
      <label for="enviarPdf">Enviar PDF agora (opcional)</label>
    </div>
    <div class="note">Se envia o PDF agora, podemos imprimir mais rápido</div>

    <label for="frete">Frete:</label>
    <select id="frete" onchange="mostrarLocalizacao()">
      <option value="0">Sem frete</option>
      <option value="2">Com frete: R$ 2,00</option>
    </select>

    <div id="localizacao">
      <label for="endereco">Endereço para entrega:</label>
      <input type="text" id="endereco" placeholder="Digite seu endereço">
    </div>

    <label for="nome">Nome (obrigatório):</label>
    <input type="text" id="nome" placeholder="Digite seu nome" required>

    <label for="telefone">Telefone (obrigatório):</label>
    <input type="text" id="telefone" placeholder="Digite seu telefone" required>

    <button class="button" onclick="proximaEtapa()">Próxima Etapa</button>
  </div>
</div>

<script>
let agendamentos = [];
let pedido = {};

function mostrarLocalizacao() {
  const frete = document.getElementById('frete').value;
  const localDiv = document.getElementById('localizacao');
  localDiv.style.display = frete === '2' ? 'block' : 'none';
}

function proximaEtapa() {
  const nome = document.getElementById('nome').value.trim();
  const telefone = document.getElementById('telefone').value.trim();
  if (!nome || !telefone) {
    alert('Por favor, preencha Nome e Telefone.');
    return;
  }

  pedido.nome = nome;
  pedido.telefone = telefone;
  pedido.enviarPdf = document.getElementById('enviarPdf').checked;
  pedido.frete = document.getElementById('frete').value;
  pedido.endereco = document.getElementById('endereco') ? document.getElementById('endereco').value : '';

  const container = document.getElementById('container');
  container.innerHTML = `
    <h1>Agendamento</h1>
    <label for='data'>Data (obrigatório):</label>
    <input type='date' id='data' required onchange='atualizarHorarios()'>
    <label for='horario'>Horário (obrigatório):</label>
    <select id='horario' required></select>

    <table id='tabelaAgendamento'>
      <thead>
        <tr><th>Data</th><th>Horário</th><th>Status</th></tr>
      </thead>
      <tbody></tbody>
    </table>

    <button class='button' onclick='finalizarAgendamento()'>Finalizar Agendamento</button>
    <div id='mensagem2' style='margin-top:25px; font-size:16px;'></div>
  `;

  atualizarHorarios();
}

function atualizarHorarios() {
  const dataSelecionada = document.getElementById('data').value;
  const selectHorario = document.getElementById('horario');
  const horarios = ['08:00','10:00','12:00','14:00','16:00','18:00','20:00'];
  selectHorario.innerHTML = '';
  horarios.forEach(h => {
    const ocupado = agendamentos.find(a => a.data === dataSelecionada && a.horario === h);
    const option = document.createElement('option');
    option.value = h;
    option.text = ocupado ? `${h} (Ocupado)` : h;
    if(ocupado){ option.disabled = true; option.className = 'ocupado'; }
    selectHorario.appendChild(option);
  });
}

function atualizarTabela() {
  const tbody = document.getElementById('tabelaAgendamento').querySelector('tbody');
  tbody.innerHTML = '';
  agendamentos.forEach(a => {
    tbody.innerHTML += `<tr><td>${a.data}</td><td>${a.horario}</td><td>Agendado</td></tr>`;
  });
}

function finalizarAgendamento() {
  const data = document.getElementById('data').value;
  const horario = document.getElementById('horario').value;

  if(!data || !horario){
    alert('Por favor, selecione Data e Horário.');
    return;
  }

  agendamentos.push({data, horario});
  atualizarTabela();

  document.getElementById('mensagem2').innerHTML = `Agendamento para ${data} às ${horario} confirmado!`;

  pedido.data = data;
  pedido.horario = horario;

  const resumo = `Resumo do pedido:\nNome: ${pedido.nome}\nTelefone: ${pedido.telefone}\nFrete: ${pedido.frete == '2' ? 'Sim' : 'Não'}\nEndereço: ${pedido.endereco}\nEnviar PDF: ${pedido.enviarPdf ? 'Sim' : 'Não'}\nData: ${pedido.data} Horário: ${pedido.horario}`;

  setTimeout(() => {
    const numeroWhatsApp = '5571982513027';
    const link = `https://wa.me/${numeroWhatsApp}?text=${encodeURIComponent(resumo)}`;
    window.location.href = link;
  }, 100);
}
</script>
</body>
</html>
