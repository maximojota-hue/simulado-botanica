# Auditoria do simulado de Botânica — instruções para a IA

Documento para outra IA auditar este simulado. Leia inteiro antes de mexer em qualquer arquivo.

---

## Histórico de versões

### v2 — 2026-11-24 (por Kiro) — publicada, commit `f54d802`

Auditoria completa + melhorias, confirmada no ar em
https://maximojota-hue.github.io/simulado-botanica/.

- **Gabarito**: os 32 gabaritos originais foram conferidos contra o material-fonte
  e estavam **todos corretos**. Nenhum gabarito mudou.
- **16 alternativas duplicadas corrigidas**: metade das questões tinha a
  alternativa `e` repetindo exatamente outra alternativa (o `verificar.js` não
  pega isso). Trocadas por distratores novos e plausíveis, com o `porque`
  reescrito. Questões: 3, 10, 11, 12, 13, 18, 19, 20, 22, 23, 24, 25, 28, 30, 31, 32.
- **+6 questões novas (32 → 38)** cobrindo lacunas do material: partes da folha,
  folha simples × composta, função das pétalas, fruto seco × carnoso, bulbo
  (cebola/alho) e mono × dicotiledônea.
- **Bizus**: todas as 38 questões ganharam um macete ("🧠 Bizu:") no campo
  `conceito`, aparecendo no feedback.
- **2 mapas mentais** (blocos `formula` em árvore): decisão dos 4 grupos (aula
  `quatro-grupos`) e ciclo gametófito↔esporófito (aula `ciclos-vida`).
- **2 erros de conteúdo corrigidos**: a aula `ciclos-vida` dizia "esporo (2n)"
  (o esporo é **n**); e "Platinha" → "plantinha".

> ⚠️ **Dois perrengues de publicação na v2** (conserte se for mexer no `publicar.sh`):
> rodando `publicar.sh` via bash no Windows, (1) o zip de distribuição saiu com
> **0 bytes** — foi preciso regerar com `Compress-Archive` e dar push manual; e
> (2) o loop de espera do Pages (`gh api .../pages/builds/latest`) estourou o
> timeout. O push em si funciona; o gargalo é o zip e a espera.

### v1 — commit `19465a7`
32 questões, 10 aulas, 3 figuras. Versão inicial, sem bizus nem mapas mentais,
com as 16 alternativas duplicadas.

---

## O comando

```bash
cd "C:/Users/jcarlos/Documents/simulado-botanica" && node verificar.js simulado.html
```

Saída esperada hoje (estado v2):

```
  Arquivo:     simulado.html
  Questoes:    38
  Aulas:       10
  Figuras:     3 questoes com diagrama
  Temas:       10
  Gabarito no arquivo: a=14  b=8  c=8  d=8  e=0

  OK: banco de questoes valido. Pode publicar.
```

**O validador só checa estrutura** (1 correta por questão, 5 alternativas, `porque` em todas, aula existente, figura válida). Ele **não checa se a biologia está certa** — isso é o trabalho da auditoria.

---

## Onde está cada coisa

| Papel | Caminho |
|---|---|
| **Arquivo a editar** | `C:\Users\jcarlos\Documents\simulado-botanica\simulado.html` |
| Cópia publicada (gerada, **não editar**) | `...\simulado-botanica\index.html` |
| Validador | `...\simulado-botanica\verificar.js` |
| Script de publicação | `...\simulado-botanica\publicar.sh` |
| Material de origem (slides + resumo) | `E:\estudos gabriel\botanica\material-de-estudo\` |
| Fotos dos slides | `E:\estudos gabriel\botanica\*.jpeg` |
| Link público | https://maximojota-hue.github.io/simulado-botanica/ |
| Repositório | https://github.com/maximojota-hue/simulado-botanica (branch `main`) |
| Commit atual | `f54d802` (v2 — 38 questões) |

---

## Estrutura do arquivo

Dentro de `simulado.html`, no `<script>`:

```js
const SVG_GRUPOS = `...`;   // 3 diagramas SVG embutidos
const SVG_CICLO  = `...`;
const SVG_ORGAOS = `...`;

const QUESTIONS = [ ... ];  // 38 questões (v2)
const LICOES    = { ... };  // 10 aulas
```

As questões 1–32 têm o campo `"_orig": N` (ordem original). As 6 questões
adicionadas na v2 (33–38) não têm `_orig` — é só um marcador de origem, não
afeta nada. A `licao` de cada questão é inline; `LICAO_POR_QUESTAO` é derivado
dela por `QUESTIONS.map(q => q.licao)`.

### Formato de cada questão

```js
{
  "tema": "Os quatro grupos",
  "enunciado": "Qual grupo de plantas NÃO possui vasos condutores?",
  "figura": SVG_GRUPOS,          // opcional, só nas questões 1, 11 e 30
  "conceito": "A chave é a ordem das novidades evolutivas...",
  "licao": "quatro-grupos",      // id em LICOES
  "alternativas": [
    {"t": "Briófitas", "ok": true,  "porque": "Só as briófitas são avasculares..."},
    {"t": "Pteridófitas", "ok": false, "porque": "Pteridófita TEM vasos condutores..."},
    // ... 5 alternativas, exatamente 1 com ok: true
  ]
}
```

### Formato de cada aula

```js
"quatro-grupos": {
  "titulo": "Os quatro grupos de plantas",
  "resumo": "A matéria inteira cabe em uma linha: vasos → semente → flor e fruto...",
  "blocos": [
    {"t":"h",        "v":"A ordem das novidades"},
    {"t":"p",        "v":"Texto explicativo com <b>negrito</b>."},
    {"t":"formula",  "v":"Texto monoespaçado", "leg":"legenda opcional"},
    {"t":"figura",   "v":SVG_GRUPOS},
    {"t":"destaque", "v":"A frase que o aluno tem de levar para a prova."},
    {"t":"lista",    "v":["item 1", "item 2"]}
  ],
  "erros": ["Erro clássico 1", "Erro clássico 2"]
}
```

---

## O que auditar (em ordem)

### 1. Gabarito — a alternativa `ok:true` está realmente correta?

Percorra as 32. Pontos que costumam estar errados em material de botânica:

| Questão | Tema | Conferir |
|---|---|---|
| 1-8 | Os quatro grupos | Briófita é a única avascular. Gimnosperma tem semente **nua**, sem fruto. A pinha é **estróbilo**, não fruto. |
| 9-10 | Xilema e floema | Xilema = seiva **bruta** (água + sais), células **mortas**. Floema = **elaborada** (açúcares), células vivas. |
| 11-17 | Ciclos de vida | Esporo = **meiose** no esporófito. Gameta = **mitose** no gametófito. Briófita domina **gametófito (n)**; vasculares dominam **esporófito (2n)**. Prótalo = gametófito. Dupla fecundação: zigoto **2n**, endosperma **3n**. |
| 18-21 | Detalhes dos grupos | 3 filos de briófitas (Bryophyta, Marchantiophyta, Anthocerophyta). Coniferophyta é o maior filo de gimnospermas. **Anthophyta é angiosperma, não gimnosperma.** |
| 22-25 | Raiz | Ordem: coifa → meristemática → lisa → **pilífera** → suberosa. Pneumatóforo = respiração. Haustório = parasita. Fasciculada = sem raiz principal. |
| 26-28 | Caule | **Batata-inglesa = caule (tubérculo)**; batata-doce = raiz tuberosa. Estipe = palmeira. Colmo = bambu/cana. Estolho = morangueiro. |
| 29 | Folha | Espinho do cacto = folha modificada, função **defesa e economia de água**. |
| 30-31 | Flor | Antera produz pólen (macho). Estigma recebe. **Ovário → fruto**, **óvulo → semente**. |
| 32 | Fruto | Laranja = carnosa + **indeiscente** + polispérmica. |

### 2. Cada `porque` descreve um erro real?

A explicação da alternativa errada precisa nomear o **erro conceitual**, não só dizer "está errado". Exemplo do que é bom:

> "Pteridófita TEM vasos condutores — é justamente a novidade evolutiva dela em relação à briófita."

E do que é ruim (não deveria existir):

> "Está incorreta."

Confira se as 128 explicações (32 × 4 erradas) fazem sentido e não se contradizem.

### 3. A aula corresponde à questão?

Cada `licao` deve ser o assunto da questão. Confira se a Q17 (dupla fecundação) abre a aula `ciclos-vida` e não outra.

### 4. As aulas estão corretas?

Leia as 10 e confira contra o material de origem em
`E:\estudos gabriel\botanica\material-de-estudo\botanica_material_de_estudo.html`.
O material foi montado a partir dos slides da professora — onde houver divergência, o slide manda.

### 5. As figuras estão certas?

Três SVG: `SVG_GRUPOS` (linha evolutiva), `SVG_CICLO` (gametófito/esporófito) e `SVG_ORGAOS` (zonas da raiz, nós do caule, antera/estigma/ovário).

Verifique por medição, não por impressão: abra o arquivo no navegador e rode no console (F12)

```js
// todas as figuras renderizam?
QUESTIONS.filter(q=>q.figura).length                     // 3
// algum texto fora do viewBox?
[...document.querySelectorAll('.figura svg')].map(svg=>{
  const vb = svg.viewBox.baseVal;
  return [...svg.querySelectorAll('text')].filter(t=>{
    const x=+t.getAttribute('x'), y=+t.getAttribute('y');
    return x<0||y<0||x>vb.width||y>vb.height;
  }).map(t=>t.textContent);
}).flat()                                                // deve ser []
```

### 6. O ciclo de vida está representado certo?

O `SVG_CICLO` deve mostrar: esporófito (2n) → **meiose** → esporos (n) → gametófito (n) → **mitose** → gametas (n) → **fecundação** → zigoto (2n). Se a seta de meiose estiver no lugar da mitose, a figura ensina errado.

---

## Testes de funcionamento (não só de conteúdo)

Abra `simulado.html` no navegador e rode no console:

```js
// integridade
QUESTIONS.length === 32                                                   // true
Object.keys(LICOES).length === 10                                         // true
QUESTIONS.every(q => q.alternativas.filter(a=>a.ok).length === 1)         // true
QUESTIONS.every(q => q.alternativas.length === 5)                         // true
QUESTIONS.every(q => q.alternativas.every(a => a.porque && a.porque.length >= 25))  // true
QUESTIONS.every(q => q.licao && LICOES[q.licao])                          // true

// o embaralhamento preserva licao e figura? (roda 20 sorteios)
(() => {
  let pL=0, pF=0;
  for (let k=0; k<20; k++) montarQuiz().forEach((q,i)=>{
    if (QUESTIONS[i].licao  && !q.licao)  pL++;
    if (QUESTIONS[i].figura && !q.figura) pF++;
  });
  return {licao_perdida:pL, figura_perdida:pF};   // ambos 0
})()
```

Na tela, nesta ordem:
1. **Iniciar simulado** → cronômetro corre a partir de 50:00.
2. Q1 tem a figura dos quatro grupos abaixo do enunciado.
3. Clicar numa alternativa → **não revela nada**, só abre "Confirmar esta resposta?".
4. **Confirmar** → marca verde/vermelho, lista as 5 explicações e mostra o botão da aula.
5. **📘 Aprender o assunto do zero** → abre a aula com o tema certo.
6. **Esc** → fecha e volta na mesma questão.
7. Largura 390 px → sem rolagem horizontal.

---

## Se precisar corrigir

1. Edite **só** `simulado.html`. Nunca `index.html` à mão — ele é sobrescrito a cada publicação.
2. Rode o validador de novo.
3. Publique:

```bash
cd "C:/Users/jcarlos/Documents/simulado-botanica"
bash publicar.sh "Auditoria: descreva o que mudou"
```

4. Confirme o que o servidor entrega:

```bash
curl -sL "https://maximojota-hue.github.io/simulado-botanica/?v=$(date +%s)" | grep -c '"ok": true'
# tem de retornar 32
```

---

## Relatório final (obrigatório)

```
GABARITO
- Questões com gabarito corrigido: [números, ou "nenhuma"]
- Alternativas cujo "porque" foi reescrito: [números + letra]

AULAS
- Aulas corrigidas: [ids]
- Divergências com o material de origem: [lista]

FIGURAS
- Textos fora do viewBox: [nenhum / lista]
- Erro conceitual na figura: [nenhum / descrição]

FUNCIONAMENTO
- verificar.js: OK / falhas
- 20 sorteios sem perda de campo: OK / falhas
- Mobile 390px: OK / problema
- Publicado e confirmado no ar: sim / não
```

---

## Regras

1. **Não altere o material de origem** em `E:\estudos gabriel\botanica\` — é a fonte.
2. **Não mexa no `simulado.html` de outros assuntos** (`simulado-fisica`, `simulado-calor`). Cada um tem seu repo.
3. **Não publique sem avisar o dono** — publicação é efeito externo.
4. Se encontrar erro de gabarito, corrija e **registre** o que mudou e por quê.
5. Onde a fonte (slide da professora) divergir de um livro, **vale o slide** — é o que cai na prova dela.
