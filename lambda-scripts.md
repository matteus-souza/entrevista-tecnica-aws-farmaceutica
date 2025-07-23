# Documentação dos Scripts AWS Lambda

## Visão Geral

Esta documentação apresenta os scripts AWS Lambda implementados na Abstergo Industries para automatização de processos e otimização de custos operacionais.

## Scripts Implementados

### 1. Processador de Arquivos S3

**Arquivo**: `s3-file-processor.py`  
**Trigger**: Upload de arquivo no bucket S3  
**Runtime**: Python 3.9

```python
import json
import boto3
import logging
from datetime import datetime

# Configuração de logging
logger = logging.getLogger()
logger.setLevel(logging.INFO)

def lambda_handler(event, context):
    """
    Processa arquivos enviados para o S3
    """
    try:
        s3_client = boto3.client('s3')
        
        for record in event['Records']:
            bucket = record['s3']['bucket']['name']
            key = record['s3']['object']['key']
            
            logger.info(f"Processando arquivo: {key} do bucket: {bucket}")
            
            # Processar arquivo
            response = process_file(s3_client, bucket, key)
            
            # Enviar notificação
            send_notification(bucket, key, response)
            
        return {
            'statusCode': 200,
            'body': json.dumps('Arquivo processado com sucesso')
        }
        
    except Exception as e:
        logger.error(f"Erro no processamento: {str(e)}")
        raise

def process_file(s3_client, bucket, key):
    """
    Lógica de processamento do arquivo
    """
    # Validar tipo de arquivo
    if not key.endswith(('.pdf', '.doc', '.docx', '.txt')):
        logger.warning(f"Tipo de arquivo não suportado: {key}")
        return False
    
    # Mover para pasta processados
    copy_source = {'Bucket': bucket, 'Key': key}
    new_key = f"processados/{key}"
    
    s3_client.copy_object(
        CopySource=copy_source,
        Bucket=bucket,
        Key=new_key
    )
    
    # Remover arquivo original
    s3_client.delete_object(Bucket=bucket, Key=key)
    
    logger.info(f"Arquivo movido para: {new_key}")
    return True

def send_notification(bucket, key, success):
    """
    Envia notificação via SNS
    """
    sns_client = boto3.client('sns')
    topic_arn = 'arn:aws:sns:us-east-1:123456789012:file-processing'
    
    status = "sucesso" if success else "falha"
    message = f"Processamento do arquivo {key} finalizado com {status}"
    
    sns_client.publish(
        TopicArn=topic_arn,
        Message=message,
        Subject='Processamento de Arquivo S3'
    )
```

### 2. Gerador de Relatórios

**Arquivo**: `report-generator.py`  
**Trigger**: EventBridge (Agendado)  
**Runtime**: Python 3.9  
**Frequência**: Diário às 08:00

```python
import json
import boto3
import pandas as pd
from datetime import datetime, timedelta
import logging

logger = logging.getLogger()
logger.setLevel(logging.INFO)

def lambda_handler(event, context):
    """
    Gera relatório diário de uso de recursos AWS
    """
    try:
        # Coletar métricas
        metrics = collect_metrics()
        
        # Gerar relatório
        report = generate_report(metrics)
        
        # Salvar no S3
        save_report_to_s3(report)
        
        # Enviar por email
        send_email_report(report)
        
        return {
            'statusCode': 200,
            'body': json.dumps('Relatório gerado com sucesso')
        }
        
    except Exception as e:
        logger.error(f"Erro na geração do relatório: {str(e)}")
        raise

def collect_metrics():
    """
    Coleta métricas do CloudWatch
    """
    cloudwatch = boto3.client('cloudwatch')
    end_time = datetime.utcnow()
    start_time = end_time - timedelta(days=1)
    
    metrics = {
        's3_requests': get_metric_data(cloudwatch, 'AWS/S3', 'NumberOfObjects', start_time, end_time),
        'lambda_invocations': get_metric_data(cloudwatch, 'AWS/Lambda', 'Invocations', start_time, end_time),
        'ec2_cpu_usage': get_metric_data(cloudwatch, 'AWS/EC2', 'CPUUtilization', start_time, end_time)
    }
    
    return metrics

def get_metric_data(cloudwatch, namespace, metric_name, start_time, end_time):
    """
    Obtém dados de métrica específica
    """
    response = cloudwatch.get_metric_statistics(
        Namespace=namespace,
        MetricName=metric_name,
        StartTime=start_time,
        EndTime=end_time,
        Period=3600,
        Statistics=['Average', 'Sum']
    )
    
    return response['Datapoints']

def generate_report(metrics):
    """
    Gera relatório formatado
    """
    report = {
        'data': datetime.now().strftime('%Y-%m-%d'),
        'resumo': {
            'total_requests_s3': sum([d['Sum'] for d in metrics['s3_requests']]),
            'total_lambda_invocations': sum([d['Sum'] for d in metrics['lambda_invocations']]),
            'cpu_medio_ec2': sum([d['Average'] for d in metrics['ec2_cpu_usage']]) / len(metrics['ec2_cpu_usage']) if metrics['ec2_cpu_usage'] else 0
        },
        'detalhes': metrics
    }
    
    return report

def save_report_to_s3(report):
    """
    Salva relatório no S3
    """
    s3_client = boto3.client('s3')
    bucket = 'abstergo-reports'
    key = f"relatorios-diarios/{datetime.now().strftime('%Y/%m/%d')}-report.json"
    
    s3_client.put_object(
        Bucket=bucket,
        Key=key,
        Body=json.dumps(report, indent=2),
        ContentType='application/json'
    )
    
    logger.info(f"Relatório salvo em: s3://{bucket}/{key}")

def send_email_report(report):
    """
    Envia relatório por email via SES
    """
    ses_client = boto3.client('ses')
    
    subject = f"Relatório Diário AWS - {report['data']}"
    body = f"""
    Relatório de Uso de Recursos AWS - {report['data']}
    
    Resumo:
    - Total de requests S3: {report['resumo']['total_requests_s3']}
    - Total de invocações Lambda: {report['resumo']['total_lambda_invocations']}
    - CPU médio EC2: {report['resumo']['cpu_medio_ec2']:.2f}%
    
    Relatório completo disponível no S3.
    """
    
    ses_client.send_email(
        Source='reports@abstergo.com',
        Destination={'ToAddresses': ['ti@abstergo.com']},
        Message={
            'Subject': {'Data': subject},
            'Body': {'Text': {'Data': body}}
        }
    )
```

### 3. Otimizador de Custos EC2

**Arquivo**: `ec2-cost-optimizer.py`  
**Trigger**: EventBridge (Agendado)  
**Runtime**: Python 3.9  
**Frequência**: A cada 4 horas

```python
import json
import boto3
import logging
from datetime import datetime, timedelta

logger = logging.getLogger()
logger.setLevel(logging.INFO)

def lambda_handler(event, context):
    """
    Otimiza custos parando instâncias EC2 não utilizadas
    """
    try:
        ec2_client = boto3.client('ec2')
        cloudwatch = boto3.client('cloudwatch')
        
        # Listar todas as instâncias em execução
        instances = get_running_instances(ec2_client)
        
        # Verificar uso de CPU
        for instance in instances:
            cpu_usage = get_cpu_usage(cloudwatch, instance['InstanceId'])
            
            if should_stop_instance(cpu_usage, instance):
                stop_instance(ec2_client, instance)
                send_notification(instance, cpu_usage)
        
        return {
            'statusCode': 200,
            'body': json.dumps('Otimização de custos executada')
        }
        
    except Exception as e:
        logger.error(f"Erro na otimização: {str(e)}")
        raise

def get_running_instances(ec2_client):
    """
    Obtém instâncias em execução
    """
    response = ec2_client.describe_instances(
        Filters=[
            {'Name': 'instance-state-name', 'Values': ['running']},
            {'Name': 'tag:Environment', 'Values': ['development', 'testing']}
        ]
    )
    
    instances = []
    for reservation in response['Reservations']:
        for instance in reservation['Instances']:
            instances.append(instance)
    
    return instances

def get_cpu_usage(cloudwatch, instance_id):
    """
    Obtém uso de CPU da instância
    """
    end_time = datetime.utcnow()
    start_time = end_time - timedelta(hours=2)
    
    response = cloudwatch.get_metric_statistics(
        Namespace='AWS/EC2',
        MetricName='CPUUtilization',
        Dimensions=[{'Name': 'InstanceId', 'Value': instance_id}],
        StartTime=start_time,
        EndTime=end_time,
        Period=300,
        Statistics=['Average']
    )
    
    return response['Datapoints']

def should_stop_instance(cpu_data, instance):
    """
    Determina se a instância deve ser parada
    """
    if not cpu_data:
        return False
    
    # Calcular CPU médio
    avg_cpu = sum([d['Average'] for d in cpu_data]) / len(cpu_data)
    
    # Verificar tags de proteção
    for tag in instance.get('Tags', []):
        if tag['Key'] == 'AutoStop' and tag['Value'] == 'false':
            return False
    
    # Parar se CPU < 5% por mais de 2 horas
    return avg_cpu < 5.0

def stop_instance(ec2_client, instance):
    """
    Para a instância EC2
    """
    instance_id = instance['InstanceId']
    
    ec2_client.stop_instances(InstanceIds=[instance_id])
    
    logger.info(f"Instância {instance_id} parada por baixo uso de CPU")

def send_notification(instance, cpu_usage):
    """
    Envia notificação sobre instância parada
    """
    sns_client = boto3.client('sns')
    
    avg_cpu = sum([d['Average'] for d in cpu_usage]) / len(cpu_usage)
    
    message = f"""
    Instância EC2 parada automaticamente:
    
    Instance ID: {instance['InstanceId']}
    Tipo: {instance['InstanceType']}
    CPU médio (2h): {avg_cpu:.2f}%
    Data/Hora: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}
    """
    
    sns_client.publish(
        TopicArn='arn:aws:sns:us-east-1:123456789012:cost-optimization',
        Message=message,
        Subject='Instância EC2 Parada - Otimização de Custos'
    )
```

## Configuração e Deployment

### Variáveis de Ambiente
- `S3_BUCKET`: Bucket para relatórios
- `SNS_TOPIC_ARN`: Tópico SNS para notificações
- `EMAIL_SENDER`: Email remetente SES

### Permissões IAM Necessárias
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:*",
        "sns:Publish",
        "ses:SendEmail",
        "cloudwatch:GetMetricStatistics",
        "ec2:DescribeInstances",
        "ec2:StopInstances"
      ],
      "Resource": "*"
    }
  ]
}
```

## Monitoramento

### Métricas Importantes
- Duração de execução
- Taxa de erro
- Número de invocações
- Custos por execução

### Logs
Todos os scripts utilizam CloudWatch Logs para monitoramento e debugging.

## Contato

**Responsável**: Mateus de Souza Paula  
**Email**: mateus.paula@abstergo.com  
**Data de Criação**: 15/07/2025
