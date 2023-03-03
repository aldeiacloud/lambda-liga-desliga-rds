Lambda em Python 3.9 para desligar uma instancia com tag RDS=desliga

`
import boto3
def lambda_handler(event, context):
    rds_client = boto3.client('rds')
    response = rds_client.describe_db_instances(
        Filters=[
            {
                'Name': 'tag:RDS',
                'Values': ['desliga']
            }
        ]
    )

    for instance in response['DBInstances']:
        rds_client.stop_db_instance(DBInstanceIdentifier=instance['DBInstanceIdentifier'])
        print(f"Instância {instance['DBInstanceIdentifier']} foi desligada.")
`

Nesta versão modificada do código, não há nenhuma lógica de agendamento. Em vez disso, a função simplesmente obtém uma lista de instâncias RDS com a tag "RDS=desliga" e desliga cada uma delas.

Para configurar a lógica de agendamento no EventBridge, você precisará criar uma regra de evento no EventBridge e configurá-la para disparar a função lambda em um horário específico. Por exemplo, você pode criar uma regra de evento que é acionada todos os dias às 19:00, e então configurar a função lambda como alvo dessa regra. Quando a regra de evento for acionada, o EventBridge chamará a função lambda para desligar as instâncias RDS.

##

Lambda em Python 3.9 para ligar uma instancia com tag RDS=desliga


`
import boto3

def lambda_handler(event, context):

    rds_client = boto3.client('rds')
    response = rds_client.describe_db_instances(
        Filters=[
            {
                'Name': 'tag:RDS',
                'Values': ['desliga']
            }
        ]
    )

    for instance in response['DBInstances']:
        rds_client.start_db_instance(DBInstanceIdentifier=instance['DBInstanceIdentifier'])
        print(f"Instância {instance['DBInstanceIdentifier']} foi ligada.")
` 

Nesta versão modificada do código, a lógica de obtenção de instâncias RDS com a tag "RDS=desliga" é a mesma. A diferença é que, em vez de chamar o método `stop_db_instance`, a função agora chama o método `start_db_instance`, o que liga a instância RDS.

Lembre-se de que você ainda precisará atribuir a política correta à função lambda para que ela tenha as permissões necessárias para ligar as instâncias RDS. Veja a minha resposta anterior para obter um exemplo de política JSON que você pode usar.

Para que a função Lambda tenha permissão para desligar as instâncias RDS usando o cliente do RDS da AWS, você precisará atribuir uma política ao papel da função que permita o acesso ao serviço RDS. Aqui está um exemplo de política JSON que você pode usar:

jsonCopy code

`{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
              "rds:StopDBInstance",
              "rds:DescribeDBInstances"
            ],
            "Resource": "*"
        }
    ]
}` 

Esta política permite que a função Lambda chame os métodos `StopDBInstance` e `DescribeDBInstances` no serviço RDS, que são necessários para desligar as instâncias RDS e obter informações sobre as instâncias com a tag "RDS=desliga".

Para atribuir essa política ao papel da função lambda, siga estas etapas:

1. Abra o console do AWS IAM (Identity and Access Management)
2. Clique em "Funções" no painel esquerdo e selecione a função da sua função Lambda
3. Clique na guia "Permissões" na parte superior da página
4. Clique no botão "Adicionar permissão"
5. Selecione "Anexar política existente"
6. Procure e selecione a política acima
7. Clique no botão "Revisar política"
8. Digite um nome descritivo para a política e clique em "Adicionar permissão"

Após atribuir a política à função Lambda, ela terá as permissões necessárias para acessar o serviço RDS e desligar as instâncias RDS com a tag "RDS=desliga".
