# Changelog

Todas as mudanças notáveis deste projeto serão documentadas aqui.
Formato baseado em [Keep a Changelog](https://keepachangelog.com/pt-BR/1.1.0/) e
versionamento [SemVer](https://semver.org/lang/pt-BR/).

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
