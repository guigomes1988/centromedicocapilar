# Centro Médico Capilar · Simulador de avaliação

Quiz de avaliação online para leads de transplante capilar vindos do Meta Ads.
O paciente responde algumas perguntas, se autoavalia na escala de Hamilton-Norwood,
envia as 5 fotos (frente, cima, nuca e laterais) e vê na hora a estimativa de enxertos,
a faixa de investimento e a condição do mês. No fim, escolhe entre falar com o comercial
pelo WhatsApp ou entrar no grupo de condições especiais.

Status: **MVP**. Arquivo único em `simulador/index.html`, sem build.

## Fluxo

1. Introdução
2. Tempo de queda
3. Faixa de idade
4. Autoavaliação Norwood (graus 1, 2, 3, 3V, 4, 5, 6, 7)
5. Fotos (opcional, com pré-análise por IA)
6. Contato (nome, WhatsApp, unidade)
7. Resultado: grau, plano ilustrativo, marcação na foto de cima, valores e promoção
8. Se "ainda não é o momento": motivo da objeção, depois grupo VIP ou consultor

## Configuração

Tudo que muda mês a mês fica no objeto `CONFIG`, no início do `<script>`:

| Campo | O que é |
|---|---|
| `whatsapp` | Número do comercial, formato `55DDNNNNNNNNN` |
| `grupo` | Link de convite do grupo de condições especiais |
| `endpoint` | URL `/exec` do Apps Script que grava o lead na planilha |
| `iaEndpoint` | URL do backend que chama a API do Claude (fotos e marcação) |
| `promo` | Título e texto da condição do mês |
| `parcelas` | Número de parcelas exibido no resultado |
| `rotacaoInicial` | Rotação inicial do molde sobre a foto de cima (180 = testa para baixo) |
| `tabela` | Enxertos e faixa de preço por grau |

### Pendências antes de publicar

- [ ] Número do WhatsApp do comercial
- [ ] Link do grupo
- [ ] Tabela real de enxertos e preços por grau (os valores atuais são de exemplo)
- [ ] Condição promocional do mês
- [ ] Lista de unidades no campo "Unidade mais perto de você"
- [ ] Endpoint do Apps Script
- [ ] IDs do GTM e do Pixel do Meta
- [ ] Validação do texto pelo médico responsável (Resolução CFM 2.336/2023)
- [ ] Consentimento LGPD específico para fotos e análise por IA

## IA

- **Pré-análise das fotos:** sugere grau, confiança, qualidade da área doadora e fotos a refazer.
- **Marcação na foto de cima:** desenha a área frontal, a coroa e a nova linha frontal sobre a foto.

Fora do Claude.ai essas funções só funcionam com `CONFIG.iaEndpoint` preenchido.
A chave da API nunca deve ir para a página: o backend recebe as fotos (`multipart/form-data`),
chama a API do Claude com o mesmo prompt usado no código (`IA_PROMPT` e `MK_PROMPT`) e devolve o JSON.

Sem IA, a marcação continua funcionando no modo manual (arrastar, tamanho e rotação).

## Eventos de rastreamento

Enviados para o `dataLayer` com `lp: 'simulador-transplante'`:
`quiz_start`, `quiz_answer`, `photo_added`, `photos_skipped`, `ia_start`, `ia_done`, `ia_error`,
`ia_grau_aceito`, `generate_lead` (dispara `Lead` no Pixel), `quiz_complete`, `cta_comercial`,
`cta_nao_agora`, `objecao`, `cta_grupo`, `cta_comercial_duvida`, `mk_ajuste`, `mk_flip`,
`mk_ia_start`, `mk_ia_done`, `mk_ia_error`.

## Rodar localmente

```bash
npx serve simulador
```

---
Desenvolvido por Solo Digital Marketing de Performance.
