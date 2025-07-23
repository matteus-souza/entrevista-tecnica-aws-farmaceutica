# Manual de Uso do Amazon S3

## Índice
1. [Introdução](#introdução)
2. [Configuração Inicial](#configuração-inicial)
3. [Operações Básicas](#operações-básicas)
4. [Políticas de Ciclo de Vida](#políticas-de-ciclo-de-vida)
5. [Segurança e Permissões](#segurança-e-permissões)
6. [Monitoramento e Logs](#monitoramento-e-logs)

## Introdução

O Amazon S3 (Simple Storage Service) é um serviço de armazenamento de objetos que oferece escalabilidade, disponibilidade de dados, segurança e performance. Este manual apresenta as melhores práticas para uso na Abstergo Industries.

## Configuração Inicial

### Criação de Bucket
1. Acesse o console AWS S3
2. Clique em "Create bucket"
3. Configure o nome do bucket (único globalmente)
4. Selecione a região adequada
5. Configure as opções de segurança

### Configurações Recomendadas
- **Versionamento**: Habilitado para controle de versões
- **Criptografia**: AES-256 ou KMS
- **Logs de acesso**: Habilitados para auditoria
- **Transferência acelerada**: Para uploads grandes

## Operações Básicas

### Upload de Arquivos
```bash
# Via CLI
aws s3 cp arquivo.txt s3://meu-bucket/
aws s3 sync ./pasta-local s3://meu-bucket/pasta-destino
```

### Download de Arquivos
```bash
# Via CLI
aws s3 cp s3://meu-bucket/arquivo.txt ./
aws s3 sync s3://meu-bucket/pasta ./pasta-local
```

### Listagem de Objetos
```bash
# Listar objetos no bucket
aws s3 ls s3://meu-bucket/
aws s3 ls s3://meu-bucket/ --recursive
```

## Políticas de Ciclo de Vida

### Configuração Automática
- **Standard**: Arquivos acessados frequentemente (0-30 dias)
- **Standard-IA**: Arquivos acessados ocasionalmente (30-90 dias)
- **Glacier**: Arquivos raramente acessados (90+ dias)
- **Deep Archive**: Arquivos para backup de longo prazo (1+ anos)

### Exemplo de Política
```json
{
  "Rules": [
    {
      "ID": "TransicaoAutomatica",
      "Status": "Enabled",
      "Transitions": [
        {
          "Days": 30,
          "StorageClass": "STANDARD_IA"
        },
        {
          "Days": 90,
          "StorageClass": "GLACIER"
        },
        {
          "Days": 365,
          "StorageClass": "DEEP_ARCHIVE"
        }
      ]
    }
  ]
}
```

## Segurança e Permissões

### Políticas de Bucket
- Configurar políticas IAM adequadas
- Usar MFA para operações críticas
- Implementar políticas de bucket restritivas
- Habilitar CloudTrail para auditoria

### Exemplo de Política IAM
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::meu-bucket/*"
    }
  ]
}
```

## Monitoramento e Logs

### Métricas Importantes
- **Requests**: Número de requisições
- **DataTransfer**: Volume de dados transferidos
- **Storage**: Tamanho total armazenado
- **Errors**: Taxa de erros

### CloudWatch Alarms
- Configurar alertas para uso excessivo
- Monitorar custos mensais
- Alertas para tentativas de acesso não autorizado

## Boas Práticas

1. **Naming Convention**: Use nomes descritivos e organizados
2. **Backup**: Mantenha backups em regiões diferentes
3. **Limpeza**: Configure políticas de exclusão automática
4. **Custos**: Monitore regularmente os custos de armazenamento
5. **Performance**: Use CloudFront para melhor performance global

## Contato e Suporte

Para dúvidas técnicas, entre em contato com a equipe de TI da Abstergo Industries.

**Responsável**: Mateus de Souza Paula  
**Email**: mateus.paula@abstergo.com  
**Data de Criação**: 15/07/2025
