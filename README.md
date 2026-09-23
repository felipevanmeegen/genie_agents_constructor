# Genie Agents Constructor — Squad Odin

Ferramenta de página única (HTML/CSS/JS, sem backend) para acelerar a migração de regras de negócio do Power BI para salas Genie no Databricks.

Tudo roda no navegador — nenhum dado é enviado a servidor nenhum. Os CSVs que você cola/carrega ficam só na sua máquina.

## O que a ferramenta faz

Três estações, seguindo o método descrito na documentação do projeto:

1. **Tabelas** — cole o CSV de consultas (extraído via `TMSCHEMA_PARTITIONS`) e a ferramenta classifica cada uma como tabela física simples, complexa, virtual (só existe no Power Query) ou seletor de interface. Gera Sources, um esqueleto de Instructions, Example Queries candidatas, um dicionário de valores por coluna, e inclui um ciclo de análise por IA (Copilot/Genie Code) para traduzir a lógica de negócio das tabelas complexas/virtuais.

2. **Relacionamentos** — cole a saída de `INFO.VIEW.RELATIONSHIPS()` e a ferramenta cruza com o que foi classificado como virtual/seletor na Estação 1, excluindo automaticamente o que não pode virar Join formal.

3. **Métricas** — cole o CSV de medidas (`INFO.VIEW.MEASURES()`) e as perguntas de negócio. A ferramenta monta o grafo de dependência entre medidas, verifica coluna por coluna se cada uma pode virar Measure formal do Genie (cruzando com o que foi analisado na Estação 1), sugere o roteamento pergunta→medida, e inclui o mesmo ciclo de análise por IA para traduzir DAX em regra de negócio.

Cada estação termina em um único prompt pronto para colar no **Genie Code**, que cria Sources, Instructions, Example Queries e Measures na sala.

## Como usar

1. Abra `index.html` num navegador (ou publique via GitHub Pages — veja abaixo).
2. Siga as 3 estações na ordem: Tabelas → Relacionamentos → Métricas.
3. Para tabelas/medidas complexas, use o painel "Análise (IA)": copie o prompt único gerado (cobre todas as tabelas/medidas pendentes de uma vez, pedindo resposta em seções por nome), rode no Copilot ou no Genie Code, cole a resposta completa de volta — o app separa automaticamente qual trecho pertence a qual tabela/medida.
4. Ao escrever a descrição de uma Example Query, o app avisa se ela ficou longa ou com várias variações entre aspas — títulos longos prejudicam o mecanismo de busca do Genie na hora de escolher qual exemplo usar; prefira uma pergunta única e curta, cobrindo variações na Instructions em vez do título.
5. Copie o prompt final de cada estação e cole no Genie Code da sua sala Genie.

## Publicar via GitHub Pages

1. Faça upload deste repositório (ou só do `index.html`) para o GitHub.
2. Vá em **Settings → Pages** do repositório.
3. Em **Source**, selecione a branch (geralmente `main`) e a pasta `/ (root)`.
4. Salve. Em alguns minutos, a página fica disponível em `https://<seu-usuario-ou-org>.github.io/<nome-do-repositorio>/`.

## Limitações conhecidas

- A extração de regras de negócio (Estação 1) e a tradução de DAX (Estação 3) são **mecânicas** — a ferramenta não interpreta lógica de negócio sozinha. Use o ciclo de "Análise (IA)" embutido em cada estação para isso.
- A checagem de "Measure formal vs Instructions" na Estação 3 depende de as tabelas relevantes já terem sido processadas na Estação 1 — sem isso, a checagem de coluna calculada fica cega para aquela tabela.
- Sem persistência entre sessões: ao recarregar a página, todos os campos são limpos propositalmente (para evitar que o navegador restaure dados de uma sessão anterior por engano).
