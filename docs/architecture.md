# EcoCube — Arquitetura e interfaces previstas

**Documento de projeto, ainda não validado em hardware.** A montagem, o firmware e os resultados de bancada não estão publicados neste repositório.

## Escopo do MVP

Um HUB com ESP32 DevKit, dois HLK-LD2410C de 24 GHz e um emissor IR. O sistema decide apenas “ocupada” ou “vazia”; não conta pessoas.

## Alimentação

A fonte central prevista é de **5 V DC / 2 A**. HUB e radares compartilham +5 V e GND em paralelo. A entrada de alimentação do ESP32 deve ser confirmada para o modelo exato de DevKit antes da montagem.

Os radares não serão alimentados por GPIOs nem pelo USB do ESP32. O dimensionamento precisa ser conferido com o consumo real, o comprimento dos cabos e a tensão medida em cada sensor sob carga.

## Interfaces dos sensores

O MVP usa VCC, GND e a saída digital OUT dos radares. A proposta atual atribui GPIO 25 ao sensor A e GPIO 26 ao sensor B; os GPIOs definitivos dependem da placa escolhida.

**Não confundir a numeração do módulo com a do RJ45.** A documentação de referência identifica OUT como pino 3 do LD2410C e UART_Tx como pino 1; conferir o módulo e seu datasheet antes de conectar.

### Conector RJ45 proprietário

Cada sensor terá um cabo CAT5e direto (*straight-through*), com a mesma pinagem nos dois extremos.

| Pino RJ45 | Função prevista | Uso no MVP |
| --- | --- | --- |
| 1 | OUT do radar | Presença digital |
| 2 | GND do sinal | Referência comum |
| 3 | Reserva para UART TX | Não conectado |
| 4 | +5 V | Alimentação do radar |
| 5 | GND | Retorno de alimentação |
| 6 | Reserva para UART RX | Não conectado |
| 7–8 | Reserva | Não conectado |

A direção dos sinais UART reservados ainda precisa ser definida antes de qualquer expansão. Eles não fazem parte da leitura digital do MVP.

**Não Ethernet / não PoE.** Identificar as portas como “SENSOR A — NÃO ETHERNET” e “SENSOR B — NÃO ETHERNET”. A pinagem deve ser conferida por continuidade; a aparência de um patch cord não substitui essa verificação.

A montagem prevista usa quatro adaptadores RJ45 para borne: dois no HUB e um em cada sensor. Uma PCB dedicada é uma possibilidade futura.

## Lógica de ocupação e comando

- Se A **ou** B detectar presença: sala ocupada e temporizador reiniciado.
- Se ambos indicarem ausência: contar um intervalo contínuo, inicialmente 10–15 minutos.
- Ao atingir o intervalo: enviar OFF por IR.

O caminho de atuação previsto é ESP32 → transistor/driver → LED IR de 940 nm → ar-condicionado. Um receptor IR será usado durante o desenvolvimento para capturar o controle. Compatibilidade, protocolo, alcance e posição precisam ser testados no aparelho escolhido.

O controle manual permanece disponível. Não há corte da rede elétrica do ar-condicionado.

## Cobertura e validação

Dois radares em lados opostos ou diagonais, com caixas não metálicas. O planejamento usa altura inicial de 1,5–2 m e inclinação para as carteiras; o posicionamento final depende da sala piloto.

O ensaio mais importante é detectar **uma única pessoa sentada e quase parada na posição menos favorável**. Também devem ser registrados presença no corredor, ventiladores, interferências entre sensores, falsos negativos e falsos positivos.

## Fora do MVP

BME280/BH1750 para ambiente, PZEM para medição energética, interface de iluminação e dashboard permanecem planejados. Não estão representados como integrações prontas.

O modelo exato da placa, as caixas, o protocolo IR, o tempo final e a economia medida continuam em aberto.
