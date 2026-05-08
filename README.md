# refactor-blueprint

Skill do Claude Code para conduzir **refactors graduais como co-piloto**: a IA propõe, discute e recomenda; você comanda. O processo gera e mantém um **blueprint vivo** em markdown, que descreve a sequência incremental do refactor e evolui conforme novas descobertas surgem durante a execução.

A skill não é um gerador de plano que termina quando o documento está pronto. Ela permanece ativa pela duração inteira do refactor — discovery, planejamento, execução comandada, mutações, conclusão.

## O que ela faz diferente

- **Macro antes de micro, sempre.** A primeira coisa que a skill estabelece é o contrato do sistema: o que aquela parte do código faz hoje, o que vai fazer depois, o que muda no contrato. Só desce em detalhes de arquivos, métodos e nomes quando o macro está travado. Refactor sem macro fechado é refactor que tecnicamente funciona mas não cumpre o objetivo.

- **Comandos explícitos para agir.** Por padrão, a skill está em modo proposta: lê código, observa, recomenda, escreve no blueprint. **Não toca em código de produção sem ordem direta** ("executa", "implementa", "aplica", "faz"). Brainstorm é seguro — divagar com a skill não vira diff.

- **Blueprint vivo.** O plano não é um documento fechado. Status de cada passo é atualizado em tempo real (`[x]` quando concluído, notas inline para descobertas neutras). Mutações estruturais (adicionar passo, reordenar, abandonar) são propostas e só aplicadas sob comando — preservando o blueprint como contrato confiável.

- **Cada passo deixa o sistema funcionando.** A invariante é que ao final de cada passo o código compila, testes passam, app sobe. Mudanças que não cabem nesse formato são decompostas. Quando uma ruptura é fisicamente inevitável (schema com downtime, troca de protocolo), o passo é marcado como **ponto de ruptura** com justificativa explícita e estratégia de mitigação.

- **Lente de convenções do projeto.** Antes e durante o discovery, a skill identifica os padrões e idioms já estabelecidos no projeto e traz pra discussão como insumo. Protege contra refactor que vira código órfão — algo que funciona mas não conversa com o resto.

- **Sessões podem ser retomadas.** Os blueprints vivem em `refactor-blueprints/<slug>.md` na raiz do projeto. Volta na sessão seguinte, invoca a skill, ela lista o que está em andamento e pergunta qual continuar.

## Instalação

Clone (ou copie) a pasta da skill para o diretório de skills do Claude Code:

```bash
git clone https://github.com/davidsgoncalves/refactor-blueprint.git ~/.claude/skills/refactor-blueprint
```

Reinicie a sessão do Claude Code. A skill passa a estar disponível para invocação.

## Uso

Invoque pelo nome da skill, com ou sem alvo:

```
/refactor-blueprint em src/auth/
```

```
/refactor-blueprint
```

Frases naturais também acionam, desde que o intent seja claro: "vamos planejar um refactor em X", "rascunha um blueprint pra refatorar Y", "estrutura essa refatoração antes de eu codar".

### O que esperar de uma sessão

1. **Abertura.** A skill faz uma leitura rápida do alvo e dos arquivos-âncora do projeto. Abre com uma observação ancorada no que viu, e uma pergunta focada — sempre no nível macro ("qual o papel desse módulo hoje, qual deve ser depois?").
2. **Discovery macro.** Você e a skill alinham o contrato antes/depois do sistema. Nada de detalhe de código nessa fase.
3. **Discovery micro.** Com o macro travado, descem em arquivos, símbolos, sequência de movimentos, testes, riscos. O blueprint é escrito ao longo do caminho — você vê ele crescendo.
4. **Execução comandada.** Quando você diz "executa o passo 1", a skill exibe em uma frase o que vai fazer, edita o código, marca o passo como concluído. Pergunta se prossegue.
5. **Mutações.** Descobertas durante a execução podem mudar o plano. A skill propõe a mutação concreta; você comanda; ela aplica e registra no histórico.
6. **Encerramento.** Quando todos os passos estão concluídos, a skill faz recap (contrato antes → depois efetivamente realizado) e propõe mover o blueprint para `refactor-blueprints/done/`.

### Estrutura do blueprint gerado

```markdown
# Refactor blueprint: <nome curto>

## Macro
**Hoje**: <papel/contrato atual>
**Depois**: <papel/contrato após o refactor>
**O que muda no contrato**: <a diferença essencial>

## Motivação
## Escopo (entra / não entra)
## Convenções aplicáveis

## Plano de execução
- [x] **Passo 1**: ...
- [ ] **Passo 2**: ...
- [ ] **Passo 3** ⚠️ **Ponto de ruptura**: ...

## Riscos e mitigações
## Alternativas consideradas
## Histórico de mutações estruturais
```

## Convenções

- **Path dos blueprints**: `refactor-blueprints/<slug>.md` na raiz do projeto.
- **`.gitignore`**: na primeira vez que o diretório é criado, a skill propõe adicionar `refactor-blueprints/` ao `.gitignore` — blueprints são artefato de trabalho individual, não documento de equipe. A decisão é sua; ela não edita o `.gitignore` sem comando.

## Documentação

A definição operacional completa da skill (princípios, fluxo, anti-padrões) está em [`SKILL.md`](./SKILL.md). É o arquivo que o Claude lê ao acionar a skill — útil pra entender em detalhe como ela se comporta.

## Licença

MIT.
