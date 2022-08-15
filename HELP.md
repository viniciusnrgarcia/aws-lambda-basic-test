
1- Cria uma nova SECRET usando AWS Secret Manager:

aws --endpoint http://localhost:4566 secretsmanager \
create-secret --name DATABASE_USER \
--description "Segredo para efetuar um teste com LocalStack" \
--secret-string "Bruce Wayne. Você descobriu o segredo do Batman" \
--region sa-east-1


output:

{
    "ARN": "arn:aws:secretsmanager:sa-east-1:000000000000:secret:DATABASE_USER-00a7aa",
    "Name": "DATABASE_USER",
    "VersionId": "9b46a40e-90f0-40d2-b107-8813b5537bfb"
}



2- Obter informação do recurso criado:



aws --endpoint http://localhost:4566 secretsmanager \
 get-secret-value \
 --secret-id DATABASE_USER \
 --region sa-east-1




3- Realizando deploy aplicação 
mvn clean install 


4- Configure profile 

aws configure --profile localstack




5- Criando IAM Role


aws --endpoint http://localhost:4566 --profile localstack \
  iam create-role \
  --region sa-east-1 \
  --role-name lambda-execution \
  --assume-role-policy-document "{\"Version\": \"2012-10-17\",\"Statement\": [{ \"Effect\": \"Allow\", \"Principal\": {\"Service\": \"lambda.amazonaws.com\"}, \"Action\": \"sts:AssumeRole\"}]}"


6- Policy de execução

aws --endpoint http://localhost:4566 --profile localstack \
  iam attach-role-policy \
  --role-name lambda-execution \
  --region sa-east-1 \
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole



7- Implantação do Lambda no Localstack


aws --endpoint http://localhost:4566 --profile localstack \
  lambda create-function \
  --function-name HelloWorld \
  --zip-file fileb://target/HelloWorld-1.0.jar \
  --handler helloworld.App \
  --region sa-east-1 \
  --runtime java11 \
  --role arn:aws:iam::000000000000:role/lambda-execution


8- Executando lambda e verificando se foi instalado corretamente:

aws --endpoint http://localhost:4566 --profile localstack \
  lambda invoke \
  --function-name HelloWorld out.txt \
  --region sa-east-1 \
  --log-type Tail


9- Removendo a função lambda criada.

 aws --endpoint http://localhost:4566 --profile localstack \
    lambda delete-function --function-name HelloWorld \
    --region sa-east-1







https://thomsdacosta.medium.com/java-aws-lambda-montando-um-ambiente-de-desenvolvimento-local-com-localstack-a845624bee40
https://reflectoring.io/aws-localstack/
https://thomsdacosta.medium.com/localstack-usando-s3-aws-secret-manager-aws-parameter-store-e-spring-boot-com-a-api-oficial-da-9e944f43a591
https://thomsdacosta.medium.com/localstack-enviando-e-consumindo-mensagens-com-sqs-sns-s3-spring-boot-e-a-api-oficial-da-marvel-21f6efab8771
