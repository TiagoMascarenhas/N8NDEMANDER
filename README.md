# 🚀 Workflows N8N - Processo Seletivo Demander

Repositório contendo os workflows N8N desenvolvidos para o processo seletivo da **Demander**, implementando uma API de busca de produtos com diferentes níveis de complexidade.

## 📋 Sobre o Projeto

Este projeto implementa uma solução de busca de produtos através de webhooks N8N, consumindo dados de uma API externa e aplicando algoritmos de similaridade para encontrar produtos relevantes.

**API de Produtos:** `https://cdn-demander.s3.sa-east-1.amazonaws.com/processo-seletivo/exemplo-de-produtos.json`

## 📁 Estrutura do Repositório

```
├── WORKFLOW SIMPLES FUNCIONANDO.json    # Versão básica (funcional)
├── WORKFLOW (esta pegando somente 1 produto da api).json    # Versão avançada (com bug conhecido)
└── README.md                            # Este arquivo
```

## 🔧 Workflows Disponíveis

### 1. **Workflow Simples** (`WORKFLOW SIMPLES FUNCIONANDO.json`)
**Status:** ✅ **Funcionando**

**Características:**
- Implementação básica e robusta
- Busca por código, código de barras ou descrição
- Tratamento robusto de diferentes formatos de resposta da API
- Sistema de fallback para garantir funcionamento
- Retorna primeiros 10 produtos se não houver critério específico

**Fluxo:**
1. **Webhook** - Recebe requisição POST em `/buscar-produtos`
2. **Buscar API** - Consulta a API de produtos
3. **Processar Final** - Aplica filtros e lógica de busca
4. **Response** - Retorna resultado formatado

### 2. **Workflow Avançado** (`WORKFLOW (esta pegando somente 1 produto da api).json`)
**Status:** ⚠️ **Com Bug Conhecido**

**Características:**
- Algoritmo avançado de similaridade de produtos
- Score de compatibilidade (0-100%)
- Busca por múltiplos critérios simultaneamente
- Tratamento de erros mais robusto
- **Bug:** Processando apenas 1 produto da API (em desenvolvimento)

**Fluxo:**
1. **Webhook** - Recebe dados de entrada
2. **Processar Dados** - Normaliza campos de entrada
3. **Consultar API** - Busca produtos externos
4. **Verificar Sucesso** - Valida retorno da API
5. **Processar Similaridade** - Calcula score de compatibilidade
6. **Response** - Retorna resultado ou erro

## 🚀 Como Usar

### Importar no N8N

1. Abra seu N8N
2. Clique em **"Import workflow"**
3. Selecione um dos arquivos JSON
4. Configure o webhook (ative o workflow)
5. Teste com requisições POST

### Exemplo de Requisição

```bash
curl -X POST http://seu-n8n.com/webhook/buscar-produtos \
  -H "Content-Type: application/json" \
  -d '{
    "produto": "Smartphone",
    "codigo": "123456",
    "codigoBarras": "7891234567890",
    "referencia": "REF-001"
  }'
```

### Parâmetros Aceitos

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `produto` | string | Nome/descrição do produto |
| `descricao` | string | Descrição alternativa |
| `codigo` | string | Código interno do produto |
| `codigoBarras` | string | Código de barras |
| `referencia` | string | Referência do produto |

## 📊 Resposta da API

### Workflow Simples
```json
{
  "sucesso": true,
  "total_produtos_api": 150,
  "total_encontrados": 5,
  "criterios_busca": {
    "produto": "Smartphone"
  },
  "produtos": [
    {
      "codigo": "123",
      "descricao": "Smartphone XYZ",
      "codigoBarras": "7891234567890",
      "referencia": "REF-001"
    }
  ]
}
```

### Workflow Avançado
```json
{
  "total_encontrados": 3,
  "total_produtos_api": 150,
  "produto_referencia": "Smartphone",
  "criterios_busca": {
    "codigo": null,
    "descricao": "Smartphone",
    "codigoBarras": null,
    "referencia": null
  },
  "produtos_similares": [
    {
      "codigo": "123",
      "descricao": "Smartphone XYZ Pro",
      "codigoBarras": "7891234567890",
      "referencia": "REF-001",
      "score_similaridade": 0.95
    }
  ]
}
```

## 🔍 Algoritmo de Similaridade (Workflow Avançado)

O workflow avançado implementa um sistema de pontuação baseado em:

- **Match exato** (código/código de barras): **100%**
- **Descrição idêntica**: **95%**
- **Descrição contida**: **80%**
- **Palavras em comum**: **até 70%**
- **Código de barras similar** (primeiros 6 dígitos): **+15%**
- **Referência idêntica**: **+10%**

**Filtro mínimo:** 30% de similaridade

## ⚠️ Problemas Conhecidos

### Workflow Avançado
- **Bug:** Processando apenas 1 produto da API em vez do array completo
- **Status:** Em investigação
- **Workaround:** Use o Workflow Simples para produção

## 🛠️ Configuração e Deploy

### Requisitos
- N8N instalado e configurado
- Acesso à internet para consultar a API externa
- Webhook habilitado no N8N

### Variáveis de Ambiente
Nenhuma configuração especial necessária. Os workflows são self-contained.

### Webhook URL
Após importar e ativar: `http://seu-n8n/webhook/buscar-produtos`

## 🧪 Testes

### Cenários de Teste Sugeridos

1. **Busca por código exato**
```json
{"codigo": "12345"}
```

2. **Busca por descrição parcial**
```json
{"produto": "Smart"}
```

3. **Busca por código de barras**
```json
{"codigoBarras": "7891234567890"}
```

4. **Busca sem critérios** (deve retornar primeiros 10)
```json
{}
```

## 📝 Desenvolvimento

### Logs e Debug
- Ambos workflows incluem logs detalhados via `console.log()`
- Use as ferramentas de debug do N8N para acompanhar execução
- O workflow simples inclui dados de debug na resposta quando necessário

### Melhorias Futuras
- [ ] Corrigir bug do array de produtos no workflow avançado
- [ ] Implementar cache para melhor performance
- [ ] Adicionar validação de entrada mais robusta
- [ ] Sistema de rate limiting
- [ ] Métricas de uso

## 👨‍💻 Desenvolvido por

**Processo Seletivo Demander**  
*Implementação de API de busca de produtos com N8N*

---

## 📞 Suporte

Para dúvidas sobre implementação ou problemas encontrados, consulte:
- Logs do N8N
- Debug do workflow via interface N8N
- Documentação oficial N8N

**Recomendação:** Use o **Workflow Simples** para ambiente de produção até correção do bug no workflow avançado.
