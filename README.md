# Dinâmica e Intensidade da Orquestra

> Aplicação interativa para o ensino de Acústica Musical · Visualização de potência sonora,
> dinâmica orquestral e percepção isofônica (Fletcher–Munson / ISO 226:2003).

### 🎓 [**Acessar a aplicação online**](https://ieysimurra.github.io/Visualizador-Interativo-de-Gama-Din-mica-de-Instrumentos-Musicais/)

[![Demo online](https://img.shields.io/badge/▶_demo_online-GitHub_Pages-2b1810?style=for-the-badge&logo=github)](https://ieysimurra.github.io/Visualizador-Interativo-de-Gama-Din-mica-de-Instrumentos-Musicais/)
[![License: MIT](https://img.shields.io/badge/license-MIT-a8762d?style=flat-square)](LICENSE)
[![Single file](https://img.shields.io/badge/HTML-single--file-c69845?style=flat-square)](index.html)
[![Web Audio API](https://img.shields.io/badge/Web_Audio_API-supported-8a4a1a?style=flat-square)](https://developer.mozilla.org/docs/Web/API/Web_Audio_API)

Aplicativo didático desenvolvido para a disciplina de **Acústica Musical** ministrada pelo
**Prof. Ivan Simurra** (NICS/UNICAMP). Permite ao aluno configurar uma orquestra (cordas,
madeiras, metais) e observar a **soma logarítmica de níveis sonoros**, contrastando o **nível
físico** (dB SPL) com a **sensação subjetiva de intensidade** (fones).

**URL da demo**: <https://ieysimurra.github.io/Visualizador-Interativo-de-Gama-Din-mica-de-Instrumentos-Musicais/>

---

## Conceitos pedagógicos cobertos

| Conceito | Onde aparece no app |
|---|---|
| Soma logarítmica de fontes sonoras independentes | Medidor de nível total (VU + leitura numérica) |
| Lei dos N performers: `L_n = L₁ + 10·log₁₀(n)` | Resposta dos contadores ± por instrumento |
| Diferenças sistemáticas entre naipes (cordas ≈ madeiras − 10 dB ≈ metais − 20 dB) | Barras de contribuição por naipe |
| Faixas dinâmicas dos instrumentos (Meyer, 1995) | Tabela de níveis por dinâmica (pp → ff) |
| Curvas isofônicas de Fletcher–Munson / ISO 226:2003 | Painel "Como o ouvido percebe a intensidade total" |
| Distinção entre dB SPL e fones | Cálculo conjugado em "Análise do Tutti" |
| Reprodução de Figura 6.4 de Henrique (tutti de 49 = 118 dB) | Preset "Tutti de Meyer" |

---

## Características principais

- **Aplicação single-page e single-file** — todo o código (HTML, CSS, JS, dados acústicos)
  em um único `index.html`. Sem dependências externas, sem bundler, sem npm.
- **Estética "concert programme"** — paleta inspirada em programas de concerto da virada do
  século (papel creme, vermelho-verniz, ouro brilhante), tipografia Cormorant Garamond /
  EB Garamond / JetBrains Mono.
- **Cálculos acústicos validados** — Tutti de Meyer reproduz exatamente 118,0 dB; ISO 226
  com erro round-trip < 0,02 dB.
- **Engine de áudio com três modos**:
  - **Tom de 1 kHz** — tom puro para calibração e demonstração das curvas isofônicas
  - **Timbre sintético** — síntese aditiva por naipe (serra/cordas, triangular/madeiras,
    quadrada/metais) com vibrato LFO
  - **Samples reais** — usa arquivos da sua biblioteca (FULLSOL, Philharmonia etc.)
- **Biblioteca de samples integrada** — parser multi-formato com drag-and-drop, suporte a
  pastas inteiras e mapeamento manual de arquivos não reconhecidos.
- **8 presets de orquestração** — Silêncio, Tutti de Meyer (49 músicos), Mozart, Beethoven,
  Mahler, Câmara, Quinteto de metais, Cordas.
- **Documentação pedagógica embutida** — objetivos, conceitos, fórmulas, plano de aula
  sugerido (3h) e bibliografia.

---

## Demo local

Não há build. Basta abrir o `index.html` num navegador moderno (Chrome, Firefox, Edge, Safari):

```bash
# Opção 1 — abrir direto
xdg-open index.html        # Linux
open index.html            # macOS
start index.html           # Windows

# Opção 2 — servidor local (recomendado para Web Audio em alguns navegadores)
python3 -m http.server 8000
# depois acesse http://localhost:8000
```

---

## Carregando samples reais

A aplicação aceita **arquivos individuais**, **pastas inteiras** ou **drag-and-drop**.

### Bancos com nomenclatura reconhecida automaticamente

| Banco | Exemplo de nome de arquivo |
|---|---|
| **FULLSOL / SOL2** (IRCAM) | `Vn-ord-A4-ff-N.wav`, `TpC-ord-G4-mf-N.wav`, `HnF-ord-D4-pp-N.wav` |
| **Philharmonia Orchestra** | `violin_A4_1_forte_arco-normal.mp3` |
| **University of Iowa MIS** | `Violin.arco.ff.sulG.A4.stereo.aif` |
| **VSL e variantes** | `Vn_susVib_A4_p.wav` |
| **Genérico** | qualquer nome contendo instrumento + nota + dinâmica |

### Por estrutura de pastas (alternativa mais robusta)

```
meus-samples/
├── violino/
│   ├── pp/A4.wav
│   ├── mf/A4.wav
│   └── ff/A4.wav
├── trompete/
│   └── mf/G4.wav
└── violoncelo/
    └── mf/E3.wav
```

A pasta define o instrumento e/ou a dinâmica; o nome do arquivo basta conter a nota.

### Abreviações reconhecidas

`vn`, `vln`, `violin`, `violino` → **Violino**
`va`, `vla`, `viola` → **Viola**
`vc`, `vlc`, `cello`, `violoncello`, `violoncelo` → **Violoncelo**
`cb`, `db`, `contrabass`, `contrabaixo` → **Contrabaixo**
`fl`, `flute`, `flauta` → **Flauta**
`ob`, `oboe`, `oboé` → **Oboé**
`cl`, `clarinet`, `clarinete`, `ClBb` → **Clarinete**
`bn`, `fg`, `bassoon`, `fagote`, `fagotto` → **Fagote**
`sax`, `saxophone`, `saxofone`, `altosax`, `tenorsax` → **Saxofone**
`hn`, `hr`, `cor`, `horn`, `trompa`, `HnF` → **Trompa**
`tp`, `tpt`, `trumpet`, `trompete`, `TpC`, `TpBb`, `BTrumpet` → **Trompete**
`tn`, `tb`, `trbn`, `trombone` → **Trombone**
`tu`, `tba`, `tuba` → **Tuba**

Dinâmicas: `pp`, `p`, `mp`, `mf`, `f`, `ff`, `pianissimo`, `piano`, `mezzo-piano`,
`mezzo-forte`, `forte`, `fortissimo`.

### Arquivos não reconhecidos

Aparecem num painel ao final da biblioteca. Para cada arquivo, escolha o instrumento e a
dinâmica nos dropdowns e clique em "→". O sample é decodificado e adicionado à biblioteca.

---

## Plano de aula sugerido (3h)

| Tempo | Bloco | Atividade |
|---|---|---|
| 0:00–0:30 | **Introdução teórica** | Soma logarítmica, intensidade vs. pressão, escala dB |
| 0:30–1:30 | **Prática guiada** | Explorar o app: contar 1, 2, 4, 8, 16 violinos a mf → confirmar lei +10·log₁₀(n) |
| 1:30–1:45 | Intervalo | |
| 1:45–2:30 | **Prática individual** | Reconstruir presets de Mozart e Mahler; comparar dB e fones |
| 2:30–3:00 | **Discussão** | Por que metais "dominam" o tutti? Implicações para orquestração |

---

## Estrutura do repositório

```
.
├── index.html                     # Aplicativo + documentação integrados
├── README.md                      # Este arquivo
├── LICENSE                        # Licença MIT
├── CHANGELOG.md                   # Histórico de versões
├── .gitignore
└── .github/
    └── workflows/
        └── deploy.yml             # Deploy automático para GitHub Pages
```

---

## Deploy

### Demo online

Esta aplicação já está publicada no GitHub Pages:

🔗 **<https://ieysimurra.github.io/Visualizador-Interativo-de-Gama-Din-mica-de-Instrumentos-Musicais/>**

### GitHub Pages (via Actions)

O workflow `.github/workflows/deploy.yml` está pré-configurado e roda automaticamente a
cada push para `main`. Para habilitar:

1. Em **Settings → Pages**, defina:
   - Source: **GitHub Actions**
2. Cada novo push para `main` dispara um novo deploy.

### Hospedagem alternativa

Como é um único arquivo HTML, basta servi-lo de qualquer servidor estático:
GitHub Pages, Netlify, Vercel, Cloudflare Pages, Surge, ou até mesmo um diretório local
servido por `python3 -m http.server`.

---

## Referências acústicas

- **Henrique, L.** (2014). *Acústica Musical* (4ª ed.). Fundação Calouste Gulbenkian.
  Capítulos 5–6 (intensidade e percepção).
- **Meyer, J.** (2009). *Acoustics and the Performance of Music* (5th ed.). Springer.
  Dados de potência sonora por instrumento.
- **Fletcher, H. & Munson, W. A.** (1933). Loudness, its definition, measurement and
  calculation. *J. Acoust. Soc. Am.*, 5(2), 82–108.
- **ISO 226:2003** *Normal equal-loudness-level contours*. International Organization
  for Standardization.

---

## Validação numérica

- **Tutti de Meyer (preset)**: 49 músicos ff → **118,0 dB SPL** (alvo: 118 dB, Fig. 6.4 de Henrique). ✓
- **ISO 226:2003 round-trip a 1 kHz**: erro < 0,02 dB. ✓
- **Cross-frequência**: 60 fones @ 100 Hz = 78,65 dB SPL; @ 3 kHz = 56,6 dB SPL (consistente com curvas publicadas). ✓
- **Parser de samples**: 33/33 testes (FULLSOL, Philharmonia, Iowa MIS, VSL, por pasta, casos extremos). ✓

---

## Créditos

**Autoria**: Prof. Ivan Simurra (NICS/UNICAMP)
**Implementação**: assistida por Claude (Anthropic)
**Licença**: MIT — veja [LICENSE](LICENSE)

> NICS · Núcleo Interdisciplinar de Comunicação Sonora
> Universidade Estadual de Campinas (UNICAMP)
