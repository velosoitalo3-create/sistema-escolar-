<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Sistema Escolar</title>

<style>
body{
  font-family:Arial;
  background:#0f172a;
  color:#fff;
  margin:0;
}

.container{
  max-width:900px;
  margin:auto;
  padding:15px;
}

.nav{
  display:flex;
  gap:10px;
}

.card{
  background:#111827;
  padding:15px;
  border-radius:12px;
  margin-top:15px;
}

input,select,button{
  width:100%;
  padding:12px;
  margin-top:10px;
  border:none;
  border-radius:10px;
  font-size:16px;
}

input,select{
  background:#1f2937;
  color:#fff;
}

button{
  background:#2563eb;
  color:#fff;
  font-weight:bold;
}

.danger{background:#dc2626}
.func{color:#22c55e}
.prob{color:#ef4444}

li{
  background:#1f2937;
  padding:10px;
  margin-top:10px;
  border-radius:10px;
}
</style>
</head>

<body>

<div class="container">

<div class="nav">
<button onclick="trocarTela('agendaTela')">📅 Agendamento</button>
<button onclick="trocarTela('noteTela')">💻 Notebooks</button>
</div>

<!-- AGENDA -->
<div id="agendaTela">
<div class="card">
<h2>Agendamento</h2>

<input id="professor" placeholder="Professor">
<input id="sala" placeholder="Sala">
<input type="date" id="data">
<input type="time" id="inicio">
<input type="time" id="fim">
<input id="carrinho" placeholder="Carrinho">

<button onclick="agendar()">Agendar</button>

<ul id="agenda"></ul>
</div>
</div>

<!-- NOTEBOOKS -->
<div id="noteTela" style="display:none">
<div class="card">
<h2>Notebooks</h2>

<input id="nome" placeholder="Nome">
<input id="qtd" type="number" placeholder="Quantidade">

<select id="status">
<option value="Funcionando">Funcionando</option>
<option value="Com Problema">Com Problema</option>
</select>

<button onclick="addNotebook()">Adicionar</button>

<h3>Total: <span id="total">0</span></h3>
<h3>Funcionando: <span id="func">0</span></h3>
<h3>Problema: <span id="prob">0</span></h3>

<h3>% Funcionando: <span id="pFunc">0%</span></h3>
<h3>% Problema: <span id="pProb">0%</span></h3>

<ul id="lista"></ul>

<button class="danger" onclick="limparTudo()">Limpar Tudo</button>
</div>
</div>

</div>

<script>

// TROCAR TELA
function trocarTela(tela){
  document.getElementById("agendaTela").style.display="none";
  document.getElementById("noteTela").style.display="none";
  document.getElementById(tela).style.display="block";
}

// DADOS
let agendaDados = JSON.parse(localStorage.getItem("agenda") || "[]");
let notes = JSON.parse(localStorage.getItem("notes") || "[]");

// SALVAR
function salvar(){
  localStorage.setItem("agenda", JSON.stringify(agendaDados));
  localStorage.setItem("notes", JSON.stringify(notes));
}

// AGENDAR
function agendar(){
  if(!professor.value || !sala.value) return alert("Preencha os campos");

  agendaDados.push({
    professor: professor.value,
    sala: sala.value,
    data: data.value,
    inicio: inicio.value,
    fim: fim.value,
    carrinho: carrinho.value
  });

  salvar();
  limparCamposAgenda();
  render();
}

// ADD NOTEBOOK 🔥 CORRIGIDO
function addNotebook(){

  let nomeInput = document.getElementById("nome").value.trim();
  let qtdInput = parseInt(document.getElementById("qtd").value);
  let statusInput = document.getElementById("status").value;

  if(!nomeInput || !qtdInput || qtdInput <= 0){
    alert("Preencha corretamente!");
    return;
  }

  for(let i = 0; i < qtdInput; i++){
    notes.push({
      nome: nomeInput,
      status: statusInput
    });
  }

  salvar();
  limparCamposNote();
  render();
}

// RENDER
function render(){

  // AGENDA
  let agendaEl = document.getElementById("agenda");
  agendaEl.innerHTML = "";

  agendaDados.forEach((a,i)=>{
    let li = document.createElement("li");
    li.innerHTML = `${a.professor} - ${a.sala} - ${a.data}
    <button onclick="removerAgenda(${i})">❌</button>`;
    agendaEl.appendChild(li);
  });

  // NOTEBOOKS
  let lista = document.getElementById("lista");
  lista.innerHTML = "";

  let total = notes.length;
  let func = 0;
  let prob = 0;

  notes.forEach((n,i)=>{
    if(n.status === "Funcionando"){
      func++;
    } else {
      prob++;
    }

    let li = document.createElement("li");
    li.innerHTML = `${n.nome} - <span class="${n.status=='Funcionando'?'func':'prob'}">${n.status}</span>
    <button onclick="removerNote(${i})">❌</button>`;
    lista.appendChild(li);
  });

  let percFunc = total ? ((func/total)*100).toFixed(1) : 0;
  let percProb = total ? ((prob/total)*100).toFixed(1) : 0;

  document.getElementById("total").textContent = total;
  document.getElementById("func").textContent = func;
  document.getElementById("prob").textContent = prob;
  document.getElementById("pFunc").textContent = percFunc + "%";
  document.getElementById("pProb").textContent = percProb + "%";
}

// REMOVER
function removerAgenda(i){
  agendaDados.splice(i,1);
  salvar();
  render();
}

function removerNote(i){
  notes.splice(i,1);
  salvar();
  render();
}

// LIMPAR
function limparTudo(){
  if(confirm("Apagar tudo?")){
    agendaDados = [];
    notes = [];
    salvar();
    render();
  }
}

// LIMPAR CAMPOS
function limparCamposAgenda(){
  professor.value="";
  sala.value="";
  carrinho.value="";
}

function limparCamposNote(){
  nome.value="";
  qtd.value="";
}

// INICIAR
render();

</script>

</body>
</html>
