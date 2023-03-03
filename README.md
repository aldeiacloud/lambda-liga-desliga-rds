Lambda em Python 3.9 para `desligar` uma ou várias instâncias RDS, informando os nomes dos bancos a serem desligados na linha `stop_rds_instances({"instances": ["my-rds-instance-1", "my-rds-instance-2"]}, {})`

```
import boto3

def stop_rds_instances(event, context):
    instances = event.get('instances', [])

    if not instances:
        return {
            'statusCode': 400,
            'body': 'Please provide a list of instances to stop'
        }

    rds_client = boto3.client('rds')
    stopped_instances = []

    for instance in instances:
        try:
            response = rds_client.stop_db_instance(DBInstanceIdentifier=instance)
            stopped_instances.append(instance)
        except rds_client.exceptions.DBInstanceNotFoundFault:
            print(f'{instance} not found')
        except rds_client.exceptions.InvalidDBInstanceStateFault:
            print(f'{instance} already stopped')

    return {
        'statusCode': 200,
        'body': f'Successfully stopped instances: {stopped_instances}'
    }

def lambda_handler(event, context):
    stop_rds_instances({"instances": ["my-rds-instance-1", "my-rds-instance-2"]}, {})

```

##
Lambda em Python 3.9 para `ligar` uma ou várias instâncias RDS, informando os nomes dos bancos a serem desligados na linha `stop_rds_instances({"instances": ["my-rds-instance-1", "my-rds-instance-2"]}, {})`


```
import boto3

def start_rds_instances(event, context):
    instances = event.get('instances', [])

    if not instances:
        return {
            'statusCode': 400,
            'body': 'Please provide a list of instances to start'
        }

    rds_client = boto3.client('rds')
    started_instances = []

    for instance in instances:
        try:
            response = rds_client.start_db_instance(DBInstanceIdentifier=instance)
            started_instances.append(instance)
        except rds_client.exceptions.DBInstanceNotFoundFault:
            print(f'{instance} not found')
        except rds_client.exceptions.InvalidDBInstanceStateFault:
            print(f'{instance} already started')

    return {
        'statusCode': 200,
        'body': f'Successfully started instances: {started_instances}'
    }

def lambda_handler(event, context):
    start_rds_instances({"instances": ["my-rds-instance-1", "my-rds-instance-2"]}, {})
```

##
Para que a função Lambda tenha permissão para desligar ou ligar as instâncias RDS usando o cliente do RDS da AWS, você precisará atribuir uma política ao papel da função que permita o acesso ao serviço RDS. Aqui está um exemplo de política JSON que você pode usar:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
              "rds:StartDBInstance",
              "rds:StopDBInstance",
              "rds:DescribeDBInstances"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
              "logs:CreateLogGroup",
              "logs:CreateLogStream",
              "logs:PutLogEvents"
            ],
            "Resource": [
              "arn:aws:logs:*:*:*"
            ]
        }
    ]
}
```

##
Esta política permite que a função Lambda chame os métodos `StopDBInstance`,`StartDBInstance` e `DescribeDBInstances` no serviço RDS, que são necessários para desligar e ligar as instâncias RDS e obter informações sobre as instâncias com a tag "RDS=desliga".

Para atribuir essa política ao papel da função lambda, siga estas etapas:

1. Abra o console do AWS IAM (Identity and Access Management)
2. Clique em "Funções" no painel esquerdo e selecione a função da sua função Lambda
3. Clique na guia "Permissões" na parte superior da página
4. Clique no botão "Adicionar permissão"
5. Selecione "Anexar política existente"
6. Procure e selecione a política acima
7. Clique no botão "Revisar política"
8. Digite um nome descritivo para a política e clique em "Adicionar permissão"

##
Obs.:<p>
Alterar o tempo de execução para 1 minuto;
Para criar o gatilho, clique em Add Trigger, escolha EventBridge e adicione um cron para a função configurada.
