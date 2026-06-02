# Changelog

Todas as mudanças notáveis deste projeto serão documentadas aqui.
Formato baseado em [Keep a Changelog](https://keepachangelog.com/pt-BR/1.1.0/) e
versionamento [SemVer](https://semver.org/lang/pt-BR/).

---

## [1.4.1] — 2026-06-02

### Corrigido

- **VU meter com escala invertida** — desde a v1.0.0, a escala do medidor
  analógico mostrava 130 dB / `ff` à **esquerda** e 40 dB / `pp` à **direita**,
  contrariando a convenção universal de instrumentos analógicos
  (valores baixos à esquerda, valores altos à direita).
  Causa: os ângulos extremos do arco (`startAngle = 200°`, `endAngle = -20°`)
  produziam direção invertida quando aplicados à função `polar()` do SVG.
  Solução: trocar os ângulos extremos para `startAngle = -20°` e `endAngle = 200°`,
  inverter o sweep flag do arco SVG de `1` para `0`, e usar `Math.abs` no
  cálculo do `largeArc` para resiliência contra a inversão de sinal.

### Verificação

Posições calculadas após correção:

| dB | Posição | Comentário |
|---|---|---|
| 40 | x=44 (esquerda) | mínimo da escala |
| 88 | x=199 (centro/topo) | região de mf |
| 118 | x=323 (direita) | Tutti Meyer · onde a agulha deve apontar |
| 130 | x=316 (direita) | máximo da escala |

---

## [1.4.0] — 2026-06-02

### Adicionado · Normalização RMS automática (Opção A)

- **Medição automática de RMS** durante o carregamento de cada sample.
  Calcula o RMS na região sustentada (descarta os primeiros e últimos 15% para
  ignorar attack/release) e armazena `rmsDbFS` e `normalizationGain` por sample.
- **Fator de correção** que iguala todos os samples a uma referência interna de
  −20 dBFS RMS. Sobre essa base nivelada, o ganho de Meyer aplicado por cima
  reproduz a relação dinâmica física correta entre pp/p/mp/mf/f/ff —
  independente da filosofia de normalização do banco fonte.
- **Limite de segurança** de ±18 dB no fator de correção, evitando amplificação
  excessiva de samples problemáticos.
- **Análise diagnóstica do banco**: o painel da biblioteca agora mostra a média
  de RMS por dinâmica e classifica automaticamente o banco como:
  - `banco preserva dinâmica natural` (diferença pp→ff ≥ 25 dB)
  - `banco parcialmente normalizado` (10 dB ≤ diferença < 25 dB)
  - `banco normalizado` (diferença < 10 dB)
- **Aplicada em quatro pontos** de playback: `playReference` (online), 
  `scheduleCalibratedGraphOffline` (renderização WAV calibrada),
  `playVoice` (online, blocos) e `scheduleBlockGraphOffline` (WAV de bloco).

### Adicionado · Validade epistêmica (Opção C)

- **Nova seção "Validade dos resultados · honestidade epistêmica"** na
  documentação pedagógica, listando explicitamente:
  - ✓ Camada matemática validada (soma logarítmica, ISO 226, Fig. 6.4 de Henrique)
  - ⚠ Tabela de níveis Meyer/Patterson como aproximação (±2-3 dB)
  - ⚠ Calibração 0 dBFS ≈ 100 dB SPL como convenção pedagógica
  - ⚠ Playback com samples e a normalização RMS aplicada
  - ✗ Aspectos não modelados (variação por registro, técnica estendida, sala, etc.)
- **Recomendação de uso** indicando que o app é apropriado para investigar
  *relações* entre configurações, e que para previsão de valores absolutos
  em situações reais, medições com sonômetro calibrado são insubstituíveis.
- **Callout discreto** no player principal lembrando que apenas comparações
  relativas são fisicamente válidas, com link para a seção completa.

### Verificação numérica

Com banco normalizado típico (pp RMS = −18 dBFS, ff RMS = −13 dBFS):
- **Sem correção (v1.3.0)**: pp→ff produzia 35 dB de diferença (esperado: 30)
- **Com correção (v1.4.0)**: pp→ff produz exatos **30,0 dB** ✓

Com banco que preserva dinâmica natural (pp RMS = −38 dBFS, ff RMS = −10 dBFS):
- **Com correção (v1.4.0)**: pp→ff produz **30,0 dB** ✓ (consistente)

---

## [1.3.0] — 2026-06-02

### Adicionado

- **Renderização e download de WAV** — exporta o áudio resultante como arquivo
  `.wav` PCM 16-bit estéreo a 48 kHz:
  - **Botão "💾 WAV"** na audição calibrada (renderiza o tom de 1 kHz, o timbre
    sintético, ou o mix de samples — conforme o modo selecionado).
  - **Botão "💾 WAV"** em cada bloco de acorde (renderiza aquele bloco isoladamente).
  - **Duração padrão**: 4 segundos com fade-in/out (0,12 s + 0,2 s).
  - **Nomes descritivos automáticos**: `audicao-calibrada-{modo}-{NN}dB.wav` e
    `bloco-{I-V}-{label-slugificado}-{NN}dB.wav`.
- **Renderização via `OfflineAudioContext`** — o mesmo grafo de áudio usado no
  playback é re-agendado num contexto offline, que renderiza em wallclock acelerado
  (4 s de áudio são produzidos em frações de segundo).
- **Codificador WAV embutido** — implementação minimalista de cabeçalho RIFF/WAVE
  + PCM 16-bit LE, sem dependências externas, totalmente single-file.

### Como funciona

Quando você clica em **💾 WAV** na audição calibrada, o app cria um
`OfflineAudioContext(2, 48000 × 4, 48000)` e re-monta a mesma cadeia de áudio
(osciladores + filtros + ganhos OU sources de samples decodificados, conforme o
modo) — mas com `start(0)` e `stop(4)` agendados explicitamente. Aguarda
`startRendering()`, codifica o `AudioBuffer` resultante em PCM 16-bit estéreo, e
dispara o download via Blob URL.

---

## [1.2.2] — 2026-06-02

### Corrigido

- **Crash de `TypeError: Cannot read properties of null (reading 'count')`** em
  `dominantFrequency`, `computeTotalLevel` (via `totalMusicians`) e `applyPreset`.
  Estas funções iteravam por `Object.keys(state)` presumindo que toda chave do
  state era um instrumento com `.count` e `.dyn`. A v1.2.0 introduziu novas chaves
  no state (`samples`, `unrecognized`, `sampleStats`, `blocks`, `blockIdCounter`,
  `blockPlayingId`, `blockPlayingSources`); ao encontrar `state.blockPlayingId`
  (que começa como `null`), o forEach quebrava com null pointer dereference.
- Solução: trocar `Object.keys(state)` por `Object.keys(INSTRUMENTS)` em todas as
  três funções — semanticamente mais correto e imune a futuras adições no state.

### Adicionado

- **Barra de erro visível no topo da página** — em vez de erros silenciosos no
  console, qualquer exceção em runtime aparece em uma barra vermelha que
  facilita reportar problemas.
- **Inicialização defensiva** — cada etapa do `init()` (renderFamilies, renderVUMeter,
  renderPresets, etc.) roda em `try/catch` independente, então uma falha numa parte
  não derruba o resto do app.

---

## [1.2.0] — 2026-06-02

### Adicionado

- **Nova seção VI: Blocos de Acordes** — atelier de orquestração para criar até 5
  configurações orquestrais independentes e comparar suas características sonoras
  lado a lado:
  - Cada bloco tem **vozes independentes** com instrumento, altura (pitch), quantidade
    e dinâmica próprias.
  - **Piano-roll SVG compacto** por bloco mostrando as vozes em sua altura real,
    com cores por família e tamanhos proporcionais ao número de músicos.
  - **Métricas por bloco**: SPL total, loudness em fones (calculada com a frequência
    dominante real do bloco), frequência dominante, balanço por naipe.
  - **Playback individual** de cada bloco com até 3,5 s de duração.
  - **Numeração romana editorial** (I, II, III, IV, V) e label editável por bloco.
- **Demonstração pedagógica central**: o mesmo conjunto de instrumentos em registros
  graves vs. agudos produz o mesmo dB SPL mas valores de fones diferentes,
  evidenciando a sensibilidade variável do ouvido pela curva isofônica.
- **Playback inteligente das vozes** — quando há samples na biblioteca, usa o sample
  mais próximo do instrumento/dinâmica solicitados com **pitch-shift** via
  `playbackRate` para a nota correta da voz (limitado a ±12 semitons para evitar
  artefatos extremos; cai para síntese fora desse range).

### Melhorado

- **Loader de samples paralelizado** — processa em lotes de 8 arquivos em paralelo,
  trazendo carregamento de bibliotecas grandes (FULLSOL ≈ milhares de arquivos)
  de minutos para segundos.
- **Progresso ao vivo** durante o carregamento — barra de status persistente
  mostrando `Decodificando X/Y (Z%) · ✓ N carregados · ? N não reconhecidos`.
- **Painel de diagnóstico** expansível mostrando os primeiros arquivos reconhecidos,
  não reconhecidos e erros de decodificação — útil quando o usuário vê "0 carregados"
  e precisa entender o porquê.
- **Mensagem final persistente** — status não some sozinho durante uploads longos
  (antes desaparecia em 4,5s mesmo se o processamento ainda estivesse em curso).
- **Compatibilidade com `decodeAudioData` callback-style** (Safari antigo) e
  Promise-style (Chrome/Firefox).
- **Limitação de exibição** a 50 arquivos não reconhecidos com contador "+ N restantes"
  para evitar travamento da UI quando há milhares de arquivos.
- **Error handling robusto** — todos os event handlers async têm `.catch()` que
  reporta erros visíveis via flashSampleStatus em vez de falhar silenciosamente.
- **Renumeração de seções**: Documentação Pedagógica passou de VI para VII para
  acomodar a nova seção Blocos.

### Corrigido

- Status de carregamento que sumia em 4,5 s mesmo durante uploads longos, fazendo
  parecer que o app travou após confirmar o pop-up de seleção de pasta.
- Loop serial `await` arquivo-por-arquivo que tornava o load de pastas grandes
  inviável na prática.

---

## [1.1.0] — 2026-06-01

### Adicionado

- **Biblioteca de samples integrada** — terceiro modo de player ("Samples") que reproduz
  arquivos de áudio reais em vez de síntese.
- **Parser multi-formato** com suporte aos bancos FULLSOL/SOL2 (IRCAM), Philharmonia
  Orchestra, University of Iowa MIS, VSL, e formatos genéricos.
- **Carregamento por estrutura de pastas** via input `webkitdirectory`.
- **Drag-and-drop recursivo** com travessia de pastas via `webkitGetAsEntry`.
- **Mapeamento manual** para arquivos não reconhecidos pelo parser automático.
- **Indicadores visuais** (dots por dinâmica) mostrando quais combinações
  instrumento × dinâmica estão disponíveis na biblioteca carregada.
- **`findBestSample(inst, dyn)`** com fallback por proximidade dinâmica
  (busca exato → ±1 → ±2 etc.).
- **Correção tímbrica** via passa-baixa quando o sample disponível não bate
  exatamente na dinâmica pedida.
- **Loop automático** para samples curtos (< 3s), com pontos de loop entre
  20% e 80% da duração para evitar attack e release.

### Testado

- 33/33 testes de parser passam (FULLSOL com qualificadores de afinação `TpC`/`TpBb`/`HnF`,
  Philharmonia com `mezzo-piano`/`mezzo-forte`, Iowa MIS com ponto-separadores, VSL,
  estrutura de pastas, casos extremos).
- Calibração acústica preservada: Tutti de Meyer continua produzindo 118,0 dB exatos.

---

## [1.0.0] — 2026-05-30

### Adicionado

- **Aplicação single-file** com seis seções: Palco, Console, Medidores,
  Fletcher-Munson, Análise do Tutti, Presets.
- **13 instrumentos modelados** em 3 famílias com tabela de níveis dB SPL
  para 6 dinâmicas (pp/p/mp/mf/f/ff), calibrados para reproduzir Figura 6.4
  de Henrique (Meyer 1995).
- **Engine de áudio** com dois modos iniciais:
  - Tom de referência a 1 kHz para calibração
  - Síntese tímbrica orquestral por família (sawtooth/cordas,
    triangular/madeiras, square/metais) com vibrato LFO e harmônicos.
- **VU meter vintage** em SVG com ponteiro animado e escala 40–130 dB.
- **Curvas isofônicas ISO 226:2003** em canvas (0/10/20/40/60/80/100 fones)
  com marcador ao vivo do ponto operativo.
- **Visualização SVG do palco** com músicos posicionados em arcos
  semicirculares, coloridos por família.
- **8 presets de orquestração**: Silêncio, Tutti de Meyer, Mozart,
  Beethoven, Mahler, Câmara, Quinteto de metais, Cordas.
- **Documentação pedagógica** completa em português embutida no app:
  objetivos, conceitos teóricos, fórmulas, plano de aula sugerido (3h),
  bibliografia.
- **Workflow GitHub Actions** para deploy automático em GitHub Pages.

### Validado

- Tutti de Meyer (49 músicos ff): 118,0 dB (alvo Henrique Fig. 6.4: 118 dB).
- ISO 226:2003 round-trip a 1 kHz: erro < 0,02 dB.
- Frequências de referência: 60 fones @ 100 Hz = 78,65 dB SPL; @ 3 kHz = 56,6 dB SPL.

---

[1.1.0]: #
[1.0.0]: #
