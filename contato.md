# Pedir orçamento

> Peça um orçamento para contratar um funcionário na Itália. Três passos curtos, resposta em até um dia útil.



<section> <span class="eyebrow">Pedir orçamento</span> <h1 id="conte-quem-voce-quer-contratar">Conte quem você quer contratar</h1> <p class="lede">Três passos curtos. Respondemos em até um dia útil com o custo completo e o esboço do contrato.</p> </section> <section> <form id="lead" class="panel" style="display:grid;gap:18px;max-width:640px" novalidate> <p id="stepinfo" class="tag" style="justify-self:start">Passo 1 de 3</p> <fieldset data-step="1" style="border:0;padding:0;margin:0;display:grid;gap:14px"> <label for="email">E-mail corporativo<input id="email" type="email" autocomplete="email" required></label> <label for="company">Empresa<input id="company" autocomplete="organization"></label> </fieldset> <fieldset data-step="2" hidden style="border:0;padding:0;margin:0;display:grid;gap:14px"> <label for="service">Do que você precisa?<select id="service"><option>Employer of Record</option><option>Folha de pagamento (já temos empresa italiana)</option><option>Visto de trabalho / Blue Card</option><option>Recrutamento</option></select></label> <label for="count">Quantas pessoas na Itália?<input id="count" type="number" min="1" value="1"></label> <label for="city">Cidade ou região<input id="city" placeholder="Milão, Roma, remoto…"></label> <label for="salary">Salário bruto anual previsto (EUR)<input id="salary" type="number" step="1000"></label> </fieldset> <fieldset data-step="3" hidden style="border:0;padding:0;margin:0;display:grid;gap:14px"> <label for="name">Seu nome<input id="name" autocomplete="name"></label> <label for="start">Data prevista de início<input id="start" type="date"></label> <label for="notes">Algo mais (a pessoa tem cidadania italiana ou europeia?)<textarea id="notes" rows="4"></textarea></label> <label style="display:flex;gap:10px;align-items:flex-start;font-weight:400"><input id="consent" type="checkbox" style="width:auto;margin-top:5px">Concordo que a ContratarNaItalia trate meus dados para responder a este pedido (política de privacidade).</label> </fieldset> <p id="msg" role="status" style="margin:0;color:var(--accent)"></p> <div class="cta"><button type="button" class="btn ghost" id="back" hidden>Voltar</button><button type="submit" class="btn" id="next">Continuar</button></div> </form> </section> <script>
// Il passo 1 salva subito l'email (lead abbandonato recuperabile dal nurturing). Endpoint del worker: /api/lead.
const f = document.getElementById('lead'); let step = 1;
// Oggetto dalla pagina partner: ?subject=Partner precompila le note.
{ const sj = new URLSearchParams(location.search).get('subject'); if (sj) document.getElementById('notes').value = '[' + sj.slice(0, 40) + '] '; }
const sets = [...f.querySelectorAll('fieldset')];
const data = () => Object.fromEntries([...f.querySelectorAll('input,select,textarea')].map(e => [e.id, e.type === 'checkbox' ? e.checked : e.value]));
async function save(final){ try { await fetch('/api/lead', {method:'POST', headers:{'Content-Type':'application/json'}, body: JSON.stringify({...data(), step, final, brand:'contratarnaitalia'})}); } catch(e){} }
function show(){ sets.forEach(s => s.hidden = +s.dataset.step !== step);
  document.getElementById('stepinfo').textContent = `Passo ${step} de 3`;
  document.getElementById('back').hidden = step === 1;
  document.getElementById('next').textContent = step === 3 ? 'Enviar pedido' : 'Continuar'; }
// Ripresa dal link nelle email: ?resume=<token> precompila i campi e salta al passo successivo.
(async () => { const tok = new URLSearchParams(location.search).get('resume'); if (!tok) return;
  try { const r = await fetch('/api/lead/' + encodeURIComponent(tok)); if (!r.ok) return; const d = await r.json();
    for (const [k, v] of Object.entries(d)) { const el = document.getElementById(k); if (el && v != null) el.value = v; }
    step = d.step || 2; show(); } catch(e){} })();
document.getElementById('back').onclick = () => { step--; show(); };
f.addEventListener('submit', e => { e.preventDefault(); const m = document.getElementById('msg'); m.textContent = '';
  if (step === 1 && !document.getElementById('email').checkValidity()) { m.textContent = 'Informe um e-mail corporativo válido para continuar.'; return; }
  if (step === 3 && !document.getElementById('consent').checked) { m.textContent = 'Marque a caixa de consentimento para que possamos responder.'; return; }
  save(step === 3);
  if (step < 3) { step++; show(); } else { f.querySelectorAll('fieldset,.cta').forEach(x => x.hidden = true); m.textContent = 'Obrigado. Responderemos em até um dia útil.'; } });
</script>


---
Source: https://contratarnaitalia.com/contato.html
