# EcoCube — Plano de validação

**Estágio: pesquisa e projeto.** Os marcos abaixo são pendentes; não representam um protótipo construído ou resultados já obtidos.

Este plano substitui o cronograma antigo, que misturava metas, percentuais sem evidência e caminhos de firmware inexistentes. A versão anterior permanece no histórico do Git.

| Marco pendente | Evidência necessária |
| --- | --- |
| Um radar e ESP32 em bancada | Modelo da placa, alimentação e leitura de OUT registrados |
| Cabeamento CAT5e / RJ45 | Pinagem conferida, tensão no sensor sob carga e leitura estável em 3–5 m |
| Dois radares e lógica OR | Registro de cobertura, presença estática, interferências e falsos positivos/negativos |
| Temporização de ausência | Teste com LED: presença reinicia o temporizador e ausência breve não aciona OFF |
| Controle infravermelho | Comando OFF reproduzido no modelo de ar escolhido, com posicionamento documentado |
| Sala piloto | Uma pessoa sentada na posição menos favorável continua detectada; nenhum desligamento indevido durante os ensaios |

## Critérios para avançar

Presença confiável vem antes do controle do ar e das expansões. Os testes devem registrar as condições da sala, posicionamento, configuração dos radares e falhas observadas.

A economia energética precisa ser medida; não há percentual de economia validado. O intervalo de 10–15 minutos é um parâmetro inicial de teste, não um valor final aprovado.

## Depois do MVP

Sensores BME280/BH1750, medição energética, iluminação e conectividade só serão integrados após a validação de presença e IR.

Referência de planejamento: documentação EcoCube v0.2 no Notion, de 29/08/2026. Revisão do portfólio: 05/10/2026.
