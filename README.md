
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Impressão Rápida</title>
<style>
  body { font-family: Arial, sans-serif; background-color: #e0f7fa; margin: 0; padding: 0; }
  .container { max-width: 720px; margin: 40px auto; background: #ffffff; padding: 40px; border-radius: 16px; }
  h1 { text-align: center; font-size: 32px; margin-bottom: 25px; }
  h2 { text-align: center; font-size: 24px; margin-bottom: 20px; color: #d32f2f; }
  label { display: block; margin: 15px 0 5px; font-weight: 600; }
  input[type=file], input[type=text], select, input[type=date] { width: 100%; padding: 12px; border-radius: 8px; border: 1px solid #bbb; box-sizing: border-box; font-size: 16px; }
  .checkbox { display: flex; align-items: center; margin-top: 12px; }
  .checkbox input { margin-right: 10px; }
  .button { display: block; width: 100%; padding: 16px; margin-top: 25px; background: #00acc1; color: #fff; border: none; border-radius: 8px; cursor: pointer; font-size: 18px; font-weight: bold; transition: 0.3s; }
  .button:hover { background: #00838f; }
  .strike { text-decoration: line-through; color: #d32f2f; font-weight: bold; font-size: 16px; }
  .note { font-size: 13px; color: #555; margin-top: 6px; }
  .aviso-lateral { flex: 1; background: #fff3f3; border: 1px solid #f5c2c2; padding: 15px; border-radius: 8px; color: #d32f2f; font-weight: bold; font-size: 15px; text-align: center; }
  table { width: 100%; border-collapse: collapse; }
  table, th, td { border: 1px solid #bbb; }
  th, td { padding: 10px; text-align: center; }
  #localizacao { display: none; margin-top: 15px; }
  .ocupado { background-color: #f8d7da; color: #721c24; }
  .flex-container { display: flex; gap: 20px; align-items: flex-start; margin-top: 20px; }
</style>
</head>
<body>
<div class="container" id="container">
  <div id="etapa1">
    <h2>🎉 Super Promoção 🎉</h2>
    <h1>Impressão Rápida</h1>

    <div class="flex-container">
      <!-- Tabela de preços -->
      <table style="flex:2;">
        <thead>
          <tr><th>Serviço</th><th>Preço</th></tr>
        </thead>
        <tbody>
          <tr><td class="strike">Impressão Comum</td><td class="strike">R$ 3,50</td></tr>
          <tr><td>Site Básico <br><small>(Obs: sem logo)</small></td><td>R$ 250,00</td></tr>
          <tr><td>Cartão de Visita</td><td>R$ 100,00 + Frete R$ 70,00</td></tr>
          <tr><td>Post no Instagram</td><td>R$ 30,00</td></tr>
          <tr><td>Contrato / Negociação</td><td>—</td></tr>
          <tr><td>Logo</td><td>R$ 20,00</td></tr>
        </tbody>
      </table>

      <!-- Aviso cima -->
      <div class="aviso-lateral">
        ⚠️ Atenção<br><br>
        Impressão Comum e Xerox<br>
        estão temporariamente<br>
        indisponíveis.
      </div>
    </div>

    <label class="strike">Xerox (Em manutenção)</label>

    <label for="servico">Escolha o serviço:</label>
    <select id="servico" required>
      <option value="">Selecione...</option>
      <option value="impressao" disabled>Impressão Comum (Indisponível)</option>
      <option value="site">Site Básico (sem logo)</option>
      <option value="cartao">Cartão de Visita</option>
      <option value="post">Post no Instagram</option>
      <option value="contrato">Contrato / Negociação</option>
      <option value="logo">Logo</option>
    </select>

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
      <option value="2">Com frete</option>
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
  const servico = document.getElementById('servico').value;

  if (!nome || !telefone || !servico) {
    alert('Por favor, preencha Nome, Telefone e Serviço.');
    return;
  }

  pedido.nome = nome;
  pedido.telefone = telefone;
  pedido.servico = servico;
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

  const resumo = `Resumo do pedido:
Nome: ${pedido.nome}
Telefone: ${pedido.telefone}
Serviço: ${pedido.servico}
Frete: ${pedido.frete == '2' ? 'Sim' : 'Não'}
Endereço: ${pedido.endereco}
Enviar PDF: ${pedido.enviarPdf ? 'Sim' : 'Não'}
⚠️ Impressão Comum e Xerox estão temporariamente indisponíveis
Data: ${pedido.data} Horário: ${pedido.horario}`;

  setTimeout(() => {
    const numeroWhatsApp = '5571982513027';
    const link = `https://wa.me/${numeroWhatsApp}?text=${encodeURIComponent(resumo)}`;
    window.location.href = link;
  }, 100);
}
</script>
</body>
</html>
